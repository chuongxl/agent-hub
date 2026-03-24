---
name: nextjs-review
description: Conduct automated deep code reviews for Next.js pull requests or branches by analyzing code conventions, architecture, complexity, performance, database queries, security issues, and best practices. Accepts PR links or branch names with automatic source/destination detection. Generates comprehensive severity-ranked review reports with quality gate pass/fail decisions. Use when reviewing Next.js PRs before merge, comparing branches, evaluating code quality, ensuring security compliance, or managing technical debt.
license: MIT
compatibility: Requires git, Node.js/npm, and network access. Works with GitHub, GitLab, and Bitbucket PR URLs. Compatible with Claude Code and GitHub Copilot.
metadata:
  author: self-learning-suite
  version: "1.0"
  category: code-review
  framework: nextjs
  difficulty: advanced
  estimated-duration: "15-30 minutes per review"
  activation-keywords: "code review, PR review, branch review, Next.js review, review rules, check PR, check branch, compare branch, audit code, quality gate, nextjs"
  supported-agents: ["Claude Code", "GitHub Copilot", "Copilot Chat"]
allowed-tools: Read Bash(git:clone,diff,log,status,checkout) Bash(npm:install) Bash(node:exec)
---

# Next.js Code Review Skill

## Overview

Automate deep code reviews for Next.js pull requests across 9 critical dimensions: conventions, architecture, complexity, performance, API routes, security, best practices, and more. Generate scored review reports with severity-based grouping and quality gate decisions.

## Core Workflow

This skill follows a 5-phase review workflow:

1. **Input Capture & Branch Setup** — Accept PR link or branch name, detect review mode, and setup branch comparison or single-branch scan
2. **Multi-Dimensional Analysis** — Scan 9 review areas simultaneously (conventions, architecture, performance, etc.)
3. **Issue Collection & Scoring** — Categorize findings by severity (Critical, High, Medium, Low, Info)
4. **Report Generation** — Create structured report with issues ranked by severity and impact
5. **Quality Gate Decision** — Determine pass/fail based on configurable thresholds

**Typical Duration**: 15-30 minutes depending on code changes size

## Step-by-Step Instructions

### Phase 1: Input Capture & Branch Setup

#### Step 1a: Capture User Input
- Ask user: "Provide a PR link, PR number, or branch name to review"
- Examples accepted:
  - `https://github.com/owner/repo/pull/123`
  - `https://gitlab.com/owner/repo/-/merge_requests/456`
  - `https://bitbucket.org/owner/repo/pull-requests/789`
  - PR number: `123` (requires repo context)
  - Branch name: `feature/auth-system`
  - Single-branch scan: `feature/auth-system --single`

#### Step 1b: Detect Input Type & Extract Information

**PR Link Detection** (regex pattern):
```
github.com.*?/pull/(\d+)  → Extract PR #
gitlab.com.*?/-/merge_requests/(\d+)  → Extract MR #
bitbucket.org.*?/pull-requests/(\d+)  → Extract PR #
```

**Branch Name Detection**:
- If input doesn't match PR pattern → treat as branch name
- Validate branch exists: `git branch -r | grep <branch-name>`

**Review Mode Detection**:
- If input contains `--single` with a branch name, run **single-branch review**.
- If input is branch name without `--single`, run **branch-diff review** against default branch (`main`/`master`).
- If input is PR URL or PR number, always run **PR comparison review** (ignore `--single` if present).

**Validation & Fallback**:
- If input is invalid/unclear, ask for clarification:
  - "Cannot parse PR link. Is this a branch name? (yes/no)"
  - "Branch not found. Did you mean: [list suggestions]?"
  - "Please provide valid input: PR link, PR number, or branch name"

#### Step 1c: Determine Source & Destination Branches

**If PR is provided**:
```bash
# Fetch PR metadata from GitHub/GitLab/Bitbucket API
# Extract:
#   - source_branch (user's feature branch)
#   - destination_branch (usually main/master or develop)
#   - repo_url, owner, repo_name
```

**If branch is provided**:
```bash
# Assume comparison against default branch (main/master)
git remote -v  # Get origin URL
git symbolic-ref refs/remotes/origin/HEAD  # Get default branch
# destination_branch = main (or master)
# source_branch = provided_branch_name
```

**If branch is provided with `--single`**:
```bash
# Single-branch scan mode (no diff baseline)
git remote -v
git fetch --all
git checkout <source_branch>
# analyze all files in the branch
git ls-tree -r --name-only HEAD
```

#### Step 1d: Setup Review Environment
- Clone repository (or update existing clone): `git clone <repo_url>` or `git pull`
- Fetch all branches: `git fetch --all`
- Switch to source branch: `git checkout <source_branch>`
- For PR / branch-diff mode:
  - Identify changed files: `git diff --name-only <destination_branch>...<source_branch>`
  - Count changes: `git diff --stat <destination_branch>...<source_branch>`
- For `--single` mode:
  - Identify all files in branch: `git ls-tree -r --name-only HEAD`
  - Count scope: total files scanned and directories covered
- **Detect project root**:
  - Look for `.codereview.config.json` in the repo root.
  - Read `project.rootFolder` from config; if absent, default to `.` (treat repo root as the Next.js root).
  - Verify the resolved folder contains a `package.json` with a `next` dependency; if missing, warn and ask the user to correct `project.rootFolder`.
  - All subsequent file analysis, path resolution, and `package.json` scanning is performed relative to `project.rootFolder`.

- Present scope summary:
  ```
  Review Type: [PR Comparison / Branch Comparison / Single Branch Scan]
  Source Branch: feature/auth-system
  Destination Branch: main (for comparison modes)
  Next.js Root: [project.rootFolder value]
  Files Changed: X files (comparison modes)
  Files Scanned: X files (single mode)
  Lines: Y added, Z deleted (comparison modes)
  Backend Folders: [resolved list]
  ```

### Phase 2: Multi-Dimensional Analysis

Analyze the code across 9 dimensions in parallel:

#### 1. **Next.js Conventions & Best Practices**
- App Router vs Pages Router consistency
- File-based routing structure compliance
- API route conventions (/api/* structure)
- Component naming and organization
- Server Components vs Client Components usage
- Layout and page component structure
- Middleware placement (middleware.ts at project root)
- Dynamic routes and segments proper usage
- Image optimization (@next/image)

#### 2. **Code Architecture & Structure**
- Component organization (components/, lib/, utils/)
- Separation of concerns (logic in custom hooks, not components)
- Custom hooks for reusable logic
- Service layer for API calls (fetch utilities)
- Data fetching patterns (getServerSideProps, getStaticProps, fetch in components)
- Error boundaries for error handling
- Context API or state management usage
- Environmental variables management (.env.local, .env.*)
- SOLID principles compliance

#### 3. **Complexity Issues**
- Component cyclomatic complexity (flag >10)
- Component size (flag components >200 lines)
- Cognitive complexity (nested loops, conditionals >15)
- JSX nesting depth (flag >5 levels)
- Props drilling (flag >3 levels deep)
- Hook dependencies array complexity
- Render function complexity

#### 4. **Performance Issues**
- Missing React.memo for expensive components
- Inefficient key usage in lists
- Missing useMemo/useCallback on dependencies
- Large bundle analysis (code splitting issues)
- Image optimization (unoptimized images)
- Font loading strategy
- CSS-in-JS vs CSS modules performance
- Missing dynamic imports for heavy components
- Client-side rendering vs SSR/SSG decisions

#### 5. **API Routes & Backend Issues**
- API route error handling
- Missing HTTP method validation
- Unvalidated request body parsing
- Missing CORS headers when needed
- Unhandled async errors in API routes
- Missing logging/monitoring
- Rate limiting not implemented
- Response structure inconsistency

#### 6. **Database & Data Fetching**
- Missing error boundaries around data fetching
- N+1 query problems (fetching in loops)
- Inefficient data fetching strategies
- Missing data validation
- Race conditions in async operations
- Missing request memoization
- Improper cache invalidation
- Migration/schema issues

#### 7. **Security Issues**
- Missing input validation/sanitization
- Hardcoded secrets/credentials
- Missing authentication checks
- Insufficient authorization checks (role-based)
- SQL injection vulnerabilities (if using database)
- XSS vulnerabilities (unsafe HTML rendering)
- Missing CSRF protection
- Unsafe file uploads
- Missing rate limiting on API routes
- Exposed sensitive data in client bundle

#### 8. **Next.js & React Best Practices**
- Proper use of Server vs Client Components
- Missing use of useCallback on event handlers
- Unnecessary state updates
- Not using proper React hooks patterns
- Missing suspense boundaries
- Error boundaries not implemented
- Accessibility issues (missing ARIA labels, alt text)
- SEO issues (missing metadata, structured data)

#### 9. **Additional Issues**
- Error handling (missing try-catch, silent failures)
- Logging coverage (missing important logs)
- Documentation (missing JSDoc for utilities)
- Testing gaps (missing unit/integration tests)
- Dependency vulnerabilities (outdated packages)
- Type safety issues (using any types)
- Environment variable documentation

### Phase 3: Issue Collection & Scoring

For each issue found, record:
- **Issue ID**: Unique identifier
- **Area**: Which of 9 dimensions (conventions, architecture, etc.)
- **Severity**: Critical | High | Medium | Low | Info
- **File & Line**: Location in code (line number is mandatory)
- **Description**: What's wrong
- **Impact**: Why it matters
- **Suggestion**: How to fix (optional, controlled by config)

**Required reporting rule**:
- Every finding must include exact line number information in both immediate results and final report output.

**Optional fix suggestion rule**:
- If a valid fix is known, include it in both immediate results and final report output.
- This behavior must be configurable from `.codereview.config.json`:

```json
{
  "project": {
    "rootFolder": "apps/web"
  },
  "output": {
    "suggestFixes": true
  },
  "backend": {
    "codeFolders": ["apps/web/src/app/api", "apps/web/src/server"],
    "includeServerActions": true
  }
}
```

- `project.rootFolder` => path (relative to repo root) to the Next.js application folder. Used in monorepo setups where Next.js lives in a subfolder such as `apps/web` or `packages/frontend`. Defaults to `.` (repo root) if not set. All other relative paths (`backend.codeFolders`, ignored paths) are resolved relative to this folder.
- `suggestFixes: true` => include fix suggestions when available.
- `suggestFixes: false` => omit fix suggestions.
- `backend.codeFolders` => paths to API routes, server actions, and server utilities, relative to `project.rootFolder`. Defaults to `["src/app/api", "pages/api", "app/api", "server"]`.
- `backend.includeServerActions` => when `true`, also treat files with `"use server"` directive as backend code (default: `true`).

**Severity Levels**:
- **Critical**: Security vulnerability, production crash risk, data loss risk
- **High**: Performance degradation, architecture violation, major bug potential
- **Medium**: Code quality, maintainability, minor performance concern
- **Low**: Style, minor naming issue, documentation gap
- **Info**: Suggestion for improvement, FYI comment

### Phase 4: Report Generation

Generate structured report with rich sections and evidence:

```markdown
# 📋 Next.js Code Review Report

**PR**: [PR Link]
**Repository**: [Repo]
**Branch**: [source branch]
**Files Changed**: [X files, +Y additions, -Z deletions]
**Issues Found**: [N total]

---

## 🎯 Quality Gate Result

### **[✅ PASS / ❌ FAIL]**

**Reason**: [Concise reason based on thresholds]

| Metric | Value | Threshold | Status |
|--------|-------|-----------|--------|
| Critical Issues | **[X]** | [allowed] | [PASS/FAIL] |
| High Issues | [X] | [allowed] | [PASS/FAIL] |
| Medium Issues | [X] | [allowed] | [PASS/FAIL] |
| Low Issues | [X] | [allowed] | [PASS/FAIL] |
| Avg Complexity | [X.X] | [max] | [PASS/FAIL] |
| Test Coverage | [X%] | [min] | [PASS/MARGINAL/FAIL] |

---

## 🔴 Critical Issues (X found)

### 1. [Issue Title]
**Area**: [Security / Performance / etc.]
**File**: `[path/file.ts:line]`
**Severity**: 🔴 Critical
**Impact**: [Business or technical risk]

**Problem**:
```typescript
// Show exact problematic snippet
```

**Why this matters**:
- [Risk 1]
- [Risk 2]

**Fix**:
```typescript
// Show concrete fixed snippet
```

**Related Rules**:
- [Rule name](references/RULESET.md#[anchor])

---

## 🟠 High Issues (X found)

### 1. [Issue Title]
**Area**: [Dimension]
**File**: `[path/file.ts:line]`
**Severity**: 🟠 High
**Impact**: [Impact statement]

**Problem**:
```typescript
// Problem snippet
```

**Metrics**:
- [Measured impact, e.g., query count, bundle delta, re-render count]

**Fix**:
```typescript
// Improved snippet
```

**Urgency**: High - fix before production deploy

---

## 🟡 Medium Issues (X found)

For medium issues, include concise entries with at least:
- Area
- File with line number
- Severity
- Impact
- Optional short fix snippet or actionable suggestion

---

## 🔵 Low Issues (X found)

Concise actionable findings with file+line and rationale.

---

## ⚪ Info (X found)

Suggestions and best-practice improvements with references.

---

## 📊 Analysis by Dimension

### 1. Next.js Conventions & Best Practices
- Status: [✅ Good / ⚠️ Issues / ❌ Critical]
- Findings: [count + one-line summary]

### 2. Code Architecture & Structure
- Status: [✅ Good / ⚠️ Issues / ❌ Critical]
- Findings: [count + one-line summary]

### 3. Complexity Issues
- Status: [✅ Good / ⚠️ Issues / ❌ Critical]
- Findings: [count + one-line summary]

### 4. Performance Issues
- Status: [✅ Good / ⚠️ Issues / ❌ Critical]
- Findings: [count + one-line summary]

### 5. API Routes & Backend Issues
- Status: [✅ Good / ⚠️ Issues / ❌ Critical]
- Findings: [count + one-line summary]

### 6. Database & Data Fetching
- Status: [✅ Good / ⚠️ Issues / ❌ Critical]
- Findings: [count + one-line summary]

### 7. Security Issues
- Status: [✅ Good / ⚠️ Issues / ❌ Critical]
- Findings: [count + one-line summary]

### 8. Next.js & React Best Practices
- Status: [✅ Good / ⚠️ Issues / ❌ Critical]
- Findings: [count + one-line summary]

### 9. Additional Issues
- Status: [✅ Good / ⚠️ Issues / ❌ Critical]
- Findings: [count + one-line summary]

---

## ✅ Recommendations

### Before Merge
1. [Must-fix blocker]
2. [Must-fix blocker]

### Before Production
1. [High-priority non-blocker]
2. [High-priority non-blocker]

### Next Sprint
1. [Maintainability improvement]
2. [Test/documentation follow-up]

---

## 📌 Next Steps
- [ ] Address all Critical issues before merge
- [ ] Create follow-up tickets for High issues
- [ ] Add tests for touched critical paths
- [ ] Re-run this review after fixes
- [ ] Attach final report to PR discussion

---

## 🧾 Appendix (Optional but Recommended)
- Scanned files list
- Rule hits per dimension
- Largest diff hotspots
- Config profile used (`startup`, `enterprise`, `fintech`, or custom)

All issues listed above must include `[File:Line]` with a concrete line number.

Each issue in Critical/High/Medium severity should prefer this detail order:
1. What is wrong (with snippet)
2. Why it matters (impact)
3. How to fix (with snippet)
4. Rule mapping (link)

When `output.suggestFixes=false`, keep the same rich structure but omit the **Fix** subsection.

```

### Phase 5: Quality Gate Decision

Evaluate against configurable thresholds:

| Threshold | Default | Configurable |
|-----------|---------|--------------|
| Max Critical Issues | 0 | Yes |
| Max High Issues | 3 | Yes |
| Max Medium Issues | 10 | Yes |
| Max Avg Complexity | 10 | Yes |
| Min Test Coverage | 70% | Yes |

**Decision Logic**:
- FAIL if: Any Critical issue found OR (High > threshold) OR (Medium > threshold) OR (Avg Complexity > threshold)
- PASS if: All thresholds within limits

### Phase 6: Report Output & Storage

#### Step 6a: Create Output Directory
- Ensure `./code-review` folder exists in the workspace root
- If it doesn't exist, create it: `mkdir -p ./code-review`
- This folder will contain all review reports and markdown files

#### Step 6b: Generate Report File
- **File Location**: `./code-review/[review-type]_[timestamp].md`
- **File Naming Convention**:
  - For PR reviews: `pr-[pr-number]_[timestamp].md` (e.g., `pr-123_2025-03-20T14-30-45.md`)
  - For branch reviews: `branch-[branch-name]_[timestamp].md` (e.g., `branch-feature-auth_2025-03-20T14-30-45.md`)
  - For single-branch scans: `scan-[branch-name]_[timestamp].md` (e.g., `scan-main_2025-03-20T14-30-45.md`)
- **Timestamp Format**: Use ISO 8601 format or `YYYY-MM-DDTHH-mm-ss`
- Output the full generated markdown report to this file

#### Step 6c: Save Supporting Files (Optional)
- If detailed analysis produces additional markdown files (e.g., separate files for each severity level, dimension analysis, etc.), save them in `./code-review` with descriptive names:
  - `./code-review/[main-report]_critical-issues.md`
  - `./code-review/[main-report]_high-issues.md`
  - `./code-review/[main-report]_summary.md`
- All supporting files should reference the main report file

#### Step 6d: Display Confirmation
- After saving, display a confirmation message:
  ```
  ✅ Review report saved to: ./code-review/[filename]
  ```
- Include the relative path from workspace root
- Provide a brief summary:
  - Total issues found
  - Quality gate result (PASS/FAIL)
  - Critical issues count

#### Step 6e: File Output Guarantee
- **Always** create and save the report to `./code-review` folder, even if:
  - The review fails quality gates
  - Issues are found
  - The repository has errors
- The report file is the primary deliverable of the review process

---

## Key Principles

- **Deep Analysis**: Don't just flag style issues—catch architectural and security problems
- **Actionable**: Every issue includes "why" and "how to fix"
- **Fast**: Complete reviews in 15-30 min for typical PRs (< 500 lines changed)
- **Transparent**: Show thresholds and reasoning for pass/fail
- **Team-Friendly**: Issues ranked by impact, easy to prioritize fixes

## When to Use This Skill

✅ **Use for**:
- Pre-merge code reviews for Next.js PRs
- Automated architecture compliance checks
- Security scanning before production deploys
- Technical debt identification
- Team code quality standards enforcement

❌ **Don't use for**:
- Functional testing (use CI/CD test suites)
- Business logic validation (owner's responsibility)
- Manual architectural design decisions (needs human discussion)

## Configuration Options

Customize per project/team via `.codereview.config.json` at the **repo root**:

- **Next.js project root** (`project.rootFolder`) — path to the Next.js app inside the repo. Required for monorepos (e.g., `apps/web`, `packages/frontend`). Defaults to `.`.
- **Backend code folders** (`backend.codeFolders`) — paths to API routes, server actions, and server utilities, relative to `project.rootFolder`. Defaults to `["src/app/api", "pages/api", "app/api", "server"]`.
- **Server actions inclusion** (`backend.includeServerActions`) — whether `"use server"` files are treated as backend code (default: `true`).
- **Quality gate thresholds** (critical/high/medium limits)
- **Complexity limits** (cyclomatic, cognitive)
- **Test coverage minimum**
- **Ignored files/paths** (node_modules, dist/, .next/, etc.)
- **Custom rules** (team-specific patterns)
- **Severity overrides** (what constitutes critical vs. high)

Minimal monorepo example:

```json
{
  "project": {
    "rootFolder": "apps/web"
  },
  "backend": {
    "codeFolders": ["apps/web/src/app/api", "apps/web/src/server"],
    "includeServerActions": true
  }
}
```

See [review rules documentation](references/RULESET.md) for all review rules, [configuration guide](references/CONFIGURATION.md) for detailed setup instructions, and [input handling guide](references/INPUT_HANDLING.md) for branch/PR detection logic.
