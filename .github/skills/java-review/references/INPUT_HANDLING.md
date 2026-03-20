# Input Handling & Branch Management

Reference guide for how the Java Review skill processes different input types.

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

**Example**:
```
User Input: https://github.com/acme/api/pull/427

Skill detects:
  → Platform: GitHub
  → PR Number: 427
  → Repo: acme/api
  → Source Branch: feature/user-service
  → Destination Branch: main
  → Comparison: feature/user-service → main
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
4. Proceeds as with full PR link

**Requirements**:
- Must be run from within Java project repository
- Repository must have upstream remote configured

### 3. Branch Names

**Direct branch name (compares to default branch)**

```
feature/user-service
fix/null-pointer
develop
```

**How it works**:
1. Skill validates branch exists: `git branch -r | grep <name>`
2. Determines default branch: `git symbolic-ref refs/remotes/origin/HEAD`
3. Sets comparison: `<branch-name> → main` (or master)
4. Performs diff and analysis

**Requirements**:
- Branch must exist in remote
- Repository must be cloned

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
    [Source/Dest Branches]


       │ NO
       ↓
┌──────────────────────────────┐
│ Treat as branch name         │
│ Validate: git branch -r      │
└──────┬───────────────────────┘
       │ Found
       ↓
   ┌──────────────────────────┐
   │ Compare to default branch│
   │ (main or master)         │
   └────────┬─────────────────┘
            ↓
    [Branch → Main Comparison]


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

## Validation & Error Handling

### Valid Inputs

```
✅ https://github.com/acme/api/pull/427
✅ https://gitlab.com/myorg/service/-/merge_requests/89
✅ https://bitbucket.org/team/service/pull-requests/56
✅ 427
✅ pr:427
✅ #427
✅ feature/user-service
✅ bugfix/null-pointer
✅ develop
```

### Invalid Inputs & Responses

#### Malformed PR URL
```
❌ Input: https://github.com/acme/api/pulls/427  (typo: pulls)
Response: "Cannot parse PR link. Please check URL format:
          github.com/owner/repo/pull/123"
Re-prompt: "Please provide valid PR link, PR number, or branch name"
```

#### Non-existent PR Number
```
❌ Input: 99999 (PR doesn't exist on GitHub)
Response: "PR #99999 not found in acme/api repository.
          Check: Is this the correct project?"
Re-prompt: "Please provide valid PR number, full PR link, or branch name"
```

#### Non-existent Branch
```
❌ Input: feature/x  (branch doesn't exist)
Response: "Branch 'feature/x' not found in repository.
          Did you mean:
          • feature/user-service
          • feature/caching
          • feature/metrics"
Re-prompt: "Please select one of the suggestions or provide full PR link"
```

#### Ambiguous Input
```
❌ Input: user  (too vague, multiple matches)
Response: "Multiple branches match 'user':
          • feature/user-service
          • feature/user-auth
          • hotfix/user-bug
          Please be more specific"
Re-prompt: "Type full branch name or provide PR link"
```

#### Network Error (Can't Reach API)
```
❌ Input: https://github.com/acme/api/pull/427
Response: "Cannot fetch PR metadata from GitHub API.
          Check: Network connection, GitHub token (if private repo)"
Fallback: "Proceeding with branch-based review if possible, or
          please retry when network is available"
```

---

## Git Operations Per Input Type

### PR Link → Setup

```bash
# 1. Clone repository
git clone https://github.com/acme/api.git
cd api

# 2. Fetch all branches (ensures we have source and destination)
git fetch --all

# 3. Checkout source branch
git checkout feature/user-service

# 4. Get list of changed files
git diff --name-only main..feature/user-service

# 5. Get change statistics
git diff --stat main..feature/user-service

# 6. Output sample
# src/main/java/com/acme/auth/UserService.java     | 45 ++++++++++++++++
# src/main/java/com/acme/auth/AuthController.java  | 23 ++++++-
# src/main/java/com/acme/auth/JwtToken.java        | 12 ++--
# 3 files changed, 70 insertions(+), 5 deletions(-)
```

### Branch Name → Setup

```bash
# 1. Fetch origin
git fetch origin

# 2. Determine default branch
git symbolic-ref refs/remotes/origin/HEAD
# Result: refs/remotes/origin/main  →  default is "main"

# 3. Checkout user's branch
git checkout feature/user-service

# 4. Get list of changed files (vs default)
git diff --name-only main..feature/user-service

# 5. Get change statistics
git diff --stat main..feature/user-service
```

---

## Comparison Modes

### Mode 1: PR-Based (Recommended)

**When**: User provides PR link or PR number

```
Source Branch: feature/user-service (user's work)
Destination Branch: main (target branch from PR)

Diff: main → feature/user-service
(Shows exactly what PR will merge)

Report Title: "PR #427: User Service Review"
  Source: feature/user-service
  Target: main
  Base: GitHub PR metadata
```

### Mode 2: Branch-Based

**When**: User provides branch name

```
Source Branch: feature/user-service (user's work)
Destination Branch: main (assumed default)

Diff: main → feature/user-service
(Shows what would be merged if PR created)

Report Title: "Branch Review: feature/user-service"
  Source: feature/user-service
  Target: main (default)
  Base: Git default branch detection
```

---

## Special Cases

### Case 1: Missing Default Branch

**Problem**: Cannot determine main/master branch

```bash
# Scenario
git symbolic-ref refs/remotes/origin/HEAD
# Result: error, symbolic ref not set

# Solution
# 1. Try common defaults: main, master, develop
git branch -r | grep "main\|master\|develop"

# 2. Ask user if multiple found or none found
```

### Case 2: Detached Branch Context

**Problem**: Running outside Git repository

```
Input: feature/user-service

Error Response:
"Cannot find Git repository.
Please:
1. Clone the repository: git clone <url>
2. Run skill from repository root
3. Or provide full PR link: https://github.com/..."
```

### Case 3: Private Repository

**Problem**: Cannot access GitHub/GitLab/Bitbucket API

```
Input: https://github.com/acme/private-api/pull/427

Error Response:
"Cannot access private repository.
Please:
1. Configure GitHub token: export GITHUB_TOKEN=...
2. Or clone locally and provide branch name instead"

Fallback:
"If already cloned locally, just provide branch name: feature/auth"
```

### Case 4: Cross-Origin Comparison

**Problem**: User wants to compare unrelated branches

```
Input: hotfix/emergency → develop
(Not a PR, just branch comparison)

Skill Response:
"Comparing unrelated branches is not a standard PR flow.
Proceeding with analysis of: hotfix/emergency changes vs develop
Note: Results show all differences, may include unrelated changes"
```

---

## Configuration: Default Comparison Branch

Customize the default branch for comparisons:

```json
{
  "review": {
    "defaultComparisonBranch": "main",  // or "master", "develop", etc
    "enableBranchReview": true,
    "enablePRReview": true
  }
}
```

---

## Summary Table

| Input Type | Format | Requires | Comparison |
|-----------|--------|----------|-----------|
| **PR Link** | Full URL | Network | Source → Destination (from PR metadata) |
| **PR Number** | 427 or pr:427 | Repo context + Network | Source → Destination (fetched from API) |
| **Branch Name** | feature/x | Git repo + branch | Branch → Default (auto-detected) |

| Input Type | Reliability | Speed | Recommended? |
|-----------|------------|-------|-------------|
| **PR Link** | High (metadata authoritative) | Slow (API call) | ✅ Yes |
| **PR Number** | High (if in correct repo) | Slow (API call) | ✅ Yes |
| **Branch Name** | Medium (assumes default) | Fast (local git) | ✅ For quick reviews |
