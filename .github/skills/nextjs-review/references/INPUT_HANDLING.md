# Input Handling & Branch Management

Reference guide for how the Next.js Code Review skill processes different input types.

---

## Input Types Supported

The skill accepts **three input types**:

### 1. PR Links (Recommended)

**Full PR URLs from GitHub, GitLab, or Bitbucket**

```
GitHub:    https://github.com/owner/repo/pull/123
GitLab:    https://gitlab.com/owner/repo/-/merge_requests/456
Bitbucket: https://bitbucket.org/owner/repo/pull-requests/789
```

**How it works**:
1. Skill extracts PR number and platform from URL
2. Uses platform API to fetch PR metadata:
   - Source branch (user's branch)
   - Destination branch (target branch, usually main/master)
   - Repository URL
3. Clones repo and sets up branches for comparison
4. Validates Next.js project structure (package.json with next dependency)

**Example**:
```
User Input: https://github.com/acme/dashboard/pull/427

Skill detects:
  → Platform: GitHub
  → PR Number: 427
  → Repo: acme/dashboard
  → Source Branch: feature/auth-redesign
  → Destination Branch: main
  → Comparison: feature/auth-redesign → main
  → Framework: Next.js (detected from package.json)
```

### 2. PR Numbers

**When repo context is available**

```
pr:427
427
#427
```

**How it works**:
1. Skill uses current git repository URL
2. Extracts owner/repo from `.git/config`
3. Fetches PR metadata from platform API
4. Validates Next.js project structure
5. Proceeds as with full PR link

**Requirements**:
- Must be run from within Next.js project repository
- Repository must have upstream remote configured
- package.json must include `next` dependency

### 3. Branch Names

**Branch comparison (default)**

```
feature/auth-system
fix/security-issue
develop
```

**How it works**:
1. Skill validates branch exists: `git branch -r | grep <name>`
2. Determines default branch: `git symbolic-ref refs/remotes/origin/HEAD`
3. Sets comparison: `<branch-name> → main` (or master)
4. Validates Next.js project structure
5. Performs diff and analysis

**Single-branch scan (`--single`)**

```
feature/auth-system --single
develop --single
```

**How it works**:
1. Skill validates branch exists: `git branch -r | grep <name>`
2. Checks out the branch
3. Enumerates all files in the branch: `git ls-tree -r --name-only HEAD`
4. Scans the full branch codebase (not only diff files)

**Requirements**:
- Branch must exist in remote
- Repository must be cloned
- package.json must include `next` dependency

---

## Input Detection Flow

```
User provides input
    ↓
┌─────────────────────────────┐
│ Does it match PR URL regex? │
└──────┬──────────────────────┘
       │ YES
       ↓
   ┌───────────────────────────┐
   │ Extract PR metadata from  │
   │ GitHub/GitLab/Bitbucket  │
   │ API                       │
   └────────┬──────────────────┘
            ↓
   ┌───────────────────────────┐
   │ Validate Next.js project  │
   │ (check package.json)      │
   └────────┬──────────────────┘
            ↓
    [Source/Dest Branches]


       │ NO
       ↓
┌──────────────────────────────┐
│ Does it match PR number      │
│ patterns (427, pr:427, #427)?│
└──────┬───────────────────────┘
       │ YES
       ↓
   ┌──────────────────────────┐
   │ Fetch repo URL from git  │
   │ Use current repo context │
   │ Query PR by number       │
   └────────┬─────────────────┘
            ↓
   ┌───────────────────────────┐
   │ Validate Next.js project  │
   │ (check package.json)      │
   └────────┬──────────────────┘
            ↓
    [Source/Dest Branches]


       │ NO
       ↓
┌──────────────────────────────┐
│ Treat as branch name         │
│ Validate: git branch -r      │
└──────┬───────────────────────┘
       │ Found
       ↓
   ┌───────────────────────────┐
   │ Has --single flag?        │
   └────────┬──────────────────┘
      │ YES                     NO
      ↓                        ↓
   ┌──────────────────────┐   ┌──────────────────────────┐
   │ Checkout branch and  │   │ Compare to default branch│
   │ scan all files       │   │ (main or master)         │
   └────────┬─────────────┘   └────────┬─────────────────┘
      ↓                          ↓
 [Single Branch Full Scan]     [Branch → Main Comparison]


       │ Not Found
       ↓
   ┌──────────────────────────┐
   │ ❌ INVALID INPUT         │
   │                          │
   │ Ask user:               │
   │ - Suggest similar        │
   │   branches               │
   │ - Ask to confirm intent  │
   │ - Re-prompt for input   │
   └──────────────────────────┘
```

---

## Validation Rules

### Next.js Project Validation

Before proceeding with analysis, skill verifies:

```bash
# 1. Check package.json exists
test -f package.json

# 2. Check Next.js is dependency
grep -q '"next"' package.json

# 3. Check app directory exists (App Router)
test -d app/  # or pages/ for Pages Router

# 4. Check tsconfig.json or jsconfig.json
test -f tsconfig.json || test -f jsconfig.json
```

**Expected structure**:
```
project/
├── package.json          ← Must have "next" dependency
├── tsconfig.json         ← Type configuration
├── app/                  ← App router (or pages/)
│   ├── page.tsx
│   └── layout.tsx
└── next.config.js        ← Next.js configuration (optional)
```

**If validation fails**:
```
❌ Not a Next.js project detected

Checked for:
  ✓ package.json exists
  ✗ "next" dependency missing
  ✓ app/ directory exists
  ✓ tsconfig.json exists

This doesn't appear to be a Next.js project.
Please ensure:
  1. npm install next
  2. Package.json includes "next" dependency
  3. This is the correct repository
```

---

## Error Handling

### Invalid PR Link
```
❌ Cannot parse PR link: https://example.com/repo/pull/123

Supported platforms:
  - GitHub: https://github.com/owner/repo/pull/123
  - GitLab: https://gitlab.com/owner/repo/-/merge_requests/123
  - Bitbucket: https://bitbucket.org/owner/repo/pull-requests/123

Please provide a valid PR link.
```

### Branch Not Found
```
❌ Branch not found: "feature/nonexistent"

Did you mean:
  - feature/auth-system
  - feature/dashboard
  - feature/api-integration

Or provide a PR link: https://github.com/owner/repo/pull/123
```

### Git Errors
```
❌ Git repository error: fatal: not a git repository

Make sure you:
  1. Are in the project root directory
  2. Have git initialized: git init
  3. Have a remote configured: git remote -v
```

### Network Errors
```
❌ Cannot fetch PR metadata (API timeout after 10s)

Possible causes:
  1. Network connectivity issue
  2. GitHub/GitLab/Bitbucket API is down
  3. Invalid GITHUB_TOKEN (if using GitHub API)

Retry or provide a branch name for local comparison.
```

---

## Authentication

### GitHub Private Repos
```bash
# Set GitHub token for private repository access
export GITHUB_TOKEN=ghp_xxxxxxxxxxxx

# Verify token has permissions:
# - repo (full control)
# - read:repo_hook
```

### GitLab Private Repos
```bash
# Set GitLab token
export GITLAB_TOKEN=glpat-xxxxxxxxxxxx

# Verify token has:
# - api scope
# - read_repository scope
```

### Bitbucket Private Repos
```bash
# Authenticate via git credentials
# Or set in environment:
export BB_TOKEN=xxxxxxxxxxxx
```

---

## Examples

### Example 1: Full PR URL
```
Input: https://github.com/acme/api/pull/427

Processing:
  ✓ Platform detected: GitHub
  ✓ PR number extracted: 427
  ✓ Repository: acme/api
  ✓ Source branch: feature/auth-system (from PR metadata)
  ✓ Destination branch: main
  ✓ Next.js validated: ✓
  
Result: Ready to analyze feature/auth-system → main
```

### Example 2: PR Number
```
Input: pr:427
(run from acme/api repo)

Processing:
  ✓ PR number detected: 427
  ✓ Repo detected: acme/api (from .git/config)
  ✓ Source branch: fix/sql-injection (from PR metadata)
  ✓ Destination branch: main
  ✓ Next.js validated: ✓
  
Result: Ready to analyze fix/sql-injection → main
```

### Example 3: Branch Name
```
Input: feature/dashboard-redesign
(run from acme/dashboard repo)

Processing:
  ✓ Branch name detected
  ✓ Branch exists: ✓
  ✓ Default branch: main
  ✓ Next.js validated: ✓
  
Result: Ready to analyze feature/dashboard-redesign → main
```

---

## Tips & Tricks

### Quickest Review
Use full PR URL - no additional context needed:
```
@nextjs-review https://github.com/myorg/app/pull/427
```

### Local Development
Use branch name when working locally:
```
# In your project directory
@nextjs-review feature/my-changes
```

### Comparing Branches
```
# Compare downstream against main
@nextjs-review upstream-feature

# Compare fork against upstream
@nextjs-review fork/my-changes
```

### CI/CD Integration
```bash
# In GitHub Actions
gh pr comment $PR --body "@nextjs-review ${{ github.event.pull_request.html_url }}"

# In GitLab CI
curl --request POST \
  --url https://gitlab.com/api/v4/projects/$CI_PROJECT_ID/merge_requests/$CI_MERGE_REQUEST_IID/notes \
  --data "body=@nextjs-review"
```
