---
name: nestjs-review
description: Conduct automated deep code reviews for NestJS pull requests or branches by analyzing code conventions, architecture, complexity, performance, SQL/database queries, security issues, and SonarQube findings. Accepts PR links or branch names with automatic source/destination detection. Generates comprehensive severity-ranked review reports with quality gate pass/fail decisions. Use when reviewing NestJS PRs before merge, comparing branches, evaluating code quality, ensuring security compliance, or managing technical debt.
license: MIT
compatibility: Requires git, Node.js/npm, and network access. Works with GitHub, GitLab, and Bitbucket PR URLs. Compatible with Claude Code and GitHub Copilot.
metadata:
  author: self-learning-suite
  version: "1.0"
  category: code-review
  framework: nestjs
  difficulty: advanced
  estimated-duration: "15-30 minutes per review"
  activation-keywords: "code review, PR review, branch review, NestJS review, review rules, check PR, check branch, compare branch, audit code, quality gate"
  supported-agents: ["Claude Code", "GitHub Copilot", "Copilot Chat"]
allowed-tools: Read Bash(git:clone,diff,log,status,checkout) Bash(npm:install) Bash(node:exec)
---

# NestJS Code Review Skill

## Overview

Automate deep code reviews for NestJS pull requests across 9 critical dimensions: conventions, architecture, complexity, performance, database queries, security, SonarQube issues, and more. Generate scored review reports with severity-based grouping and quality gate decisions.

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

- **Detect NestJS project roots (monorepo support)**:
  - Look for `.codereview.config.json` in the repo root.
  - Read `project.rootFolders` (array) from config; if absent or empty, default to `["."]` (repo root).
  - For each path in `project.rootFolders`:
    - Check the path exists in the repo; if not, warn the user and skip it.
    - Verify it contains a `package.json` with a `@nestjs/core` dependency; if missing, warn and ask the user to correct the path.
  - Only valid, confirmed NestJS folders are carried forward for analysis.
  - The full 9-dimension analysis (Phase 2) is run **independently for each valid folder**.
- Present scope summary:
  ```
  Review Type: [PR Comparison / Branch Comparison / Single Branch Scan]
  Source Branch: feature/auth-system
  Destination Branch: main (for comparison modes)
  NestJS Projects: [list of resolved rootFolders]
  Files Changed: X files across all projects (comparison modes)
  Files Scanned: X files across all projects (single mode)
  Lines: Y added, Z deleted (comparison modes)
  ```

### Phase 2: Multi-Dimensional Analysis

> **Multi-project**: Repeat this entire phase for each folder in `project.rootFolders`. Collect issues per project and merge into a unified report in Phase 4, with each issue tagged by its source project folder.

Analyze the code across 9 dimensions in parallel:

#### 1. **NestJS Conventions & Best Practices**
- Module structure (modules, providers, controllers, services)
- Decorator usage (@Module, @Controller, @Injectable, etc.)
- Dependency Injection patterns
- Naming conventions (PascalCase for classes, camelCase for methods)
- File organization (controllers/ services/ modules/ structure)

#### 2. **Code Architecture & Structure**
- Layered architecture compliance (controller → service → repository)
- Separation of concerns (business logic in services, not controllers)
- Repository/Data Access pattern usage
- Middleware and guards placement
- DTOs and validation usage
- SOLID principles compliance

#### 3. **Complexity Issues**
- Cyclomatic complexity per function (flag >10)
- Method length (flag methods >50 lines)
- Cognitive complexity (nested loops, conditionals >15)
- Class complexity (flag >20 public methods)
- Nesting depth (flag >4 levels)

#### 4. **Performance Issues**
- Synchronous blocking operations in async contexts
- Missing pagination on list endpoints
- Inefficient data transformations
- Memory leaks (event listeners not unsubscribed)
- Large object allocations in loops
- Missing caching strategies

#### 5. **SQL & Database Query Issues**
- N+1 query problems (eager loading missing)
- Missing database indexes on FK relationships
- Inefficient joins (multiple queries instead of single join)
- Missing query optimization (select specific columns)
- Transaction handling issues
- Raw SQL injection risks

#### 6. **Database Schema Issues**
- Missing constraints (NOT NULL, UNIQUE, FK)
- Poor naming conventions for columns/tables
- Missing indexes on frequently queried columns
- Denormalization without justification
- Data type mismatches
- Migration file issues

#### 7. **Security Issues**
- Missing input validation/sanitization
- Hardcoded secrets/credentials
- Missing authentication guards on endpoints
- Insufficient authorization checks (role-based access)
- SQL injection vulnerabilities
- XSS vulnerabilities (if rendering templates)
- Missing CSRF protection
- Unsafe file uploads
- Missing rate limiting

#### 8. **SonarQube-Compatible Issues**
- Code duplications (flag >3% duplication)
- Dead code (unused imports, variables)
- Type safety issues (implicit any, unknown types)
- Cognitive complexity violations
- Test coverage gaps (flag untested critical paths)
- Security hotspots (crypto usage, os commands)

#### 9. **Additional Issues**
- Error handling (missing try-catch, silent failures)
- Logging coverage (missing business-critical logs)
- Documentation (missing JSDoc for public APIs)
- Testing gaps (missing unit/integration tests)
- Dependency vulnerabilities (outdated packages)

### Phase 3: Issue Collection & Scoring

For each issue found, record:
    **Issue ID**: Unique identifier
    **Area**: Which of 9 dimensions (conventions, architecture, etc.)
    **Severity**: Critical | High | Medium | Low | Info
    **File & Line**: Location in code (must include line number)
    **Description**: What's wrong
    **Impact**: Why it matters
    **Suggestion**: How to fix (optional, configurable via config file)

**Line Number Requirement**:
  - Every issue must include the exact line number where the problem occurs in both the result and the report.

**Fix Suggestion Option**:
  - If a solution is available, suggest a fix in the result and report.
  - This feature is controlled by the config file:
    - `output.suggestFixes: true` → Show fix suggestions
    - `output.suggestFixes: false` → Omit fix suggestions

Example config:
```json
{
  "project": {
    "rootFolders": ["apps/user-service", "apps/order-service", "apps/notification-service"]
  },
  "output": {
    "format": "markdown",
    "groupByDimension": true,
    "includeSummary": true,
    "includeRecommendations": true,
    "suggestFixes": true
  }
}
```

- `project.rootFolders` — array of paths (relative to repo root) to NestJS application folders. Each folder is scanned independently. Defaults to `["."]` if not set.

Example issue output:
```
- [critical] Hardcoded secret found
  File: src/config.ts, Line: 42
  Description: API key is hardcoded
  Impact: Security risk, may leak credentials
  Suggestion: Replace with environment variable (process.env.API_KEY)
```

**Severity Levels**:
- **Critical**: Security vulnerability, production crash risk, data loss risk
- **High**: Performance degradation, architecture violation, major bug potential
- **Medium**: Code quality, maintainability, minor performance concern
- **Low**: Style, minor naming issue, documentation gap
- **Info**: Suggestion for improvement, FYI comment

### Phase 4: Report Generation

Generate structured report with sections (all issues must include line numbers; fix suggestions shown if enabled in config):

```markdown
# 📋 NestJS Code Review Report

## Summary
- PR: [PR Link]
- Repository: [Repo]
- Projects Scanned: [list of rootFolders]
- Files Changed: [X files total across all projects, Y additions, Z deletions]
- Issues Found: [Total count by severity]
- Quality Gate: [PASS/FAIL]

---

<!-- Repeat the following block for each project folder -->
## 📦 Project: [rootFolder path]

### Issues by Severity

#### 🔴 Critical Issues (X found)
1. [Issue 1] - [File:Line]
   - Description: ...
   - Impact: ...
   - Fix: ...

#### 🟠 High Issues (X found)
[Similar format]

#### 🟡 Medium Issues (X found)
[Similar format]

#### 🔵 Low Issues (X found)
[Similar format]

#### ⚪ Info (X found)
[Similar format]

### Analysis by Dimension

#### 1. NestJS Conventions & Best Practices
- Status: [✓ Good / ⚠️ Issues / ✗ Critical]
- Findings: [X issues, brief summary]

#### 2. Code Architecture & Structure
[Similar format for all 9 dimensions]

... (continue for all 9 areas)
<!-- End per-project block -->

---

## 🌐 Consolidated Summary (All Projects)

| Project | Critical | High | Medium | Low | Info | Gate |
|---------|----------|------|--------|-----|------|------|
| [rootFolder 1] | X | X | X | X | X | ✅/❌ |
| [rootFolder 2] | X | X | X | X | X | ✅/❌ |
| **Total** | **X** | **X** | **X** | **X** | **X** | **✅/❌** |

## Quality Gate

**Overall Score**: [X/100]

**Threshold Configuration**:
- Critical Issues Allowed: [X (default 0)]
- High Issues Allowed: [X (default 3)]
- Medium Issues Allowed: [X (default 10)]
- Average Complexity: [< 10 (flag if higher)]

**Decision**: [✅ PASS / ❌ FAIL]  
**Reasoning**: [Overall pass/fail — fails if ANY project fails its quality gate]

## Recommendations

1. [Priority 1 fix — include project label]
2. [Priority 2 fix — include project label]
3. [Priority 3 fix — include project label]
...

## Next Steps

- [ ] Address all Critical issues before merge
- [ ] Create follow-up issue for High issues
- [ ] Consider refactoring for complexity
- [ ] Add tests for untested paths
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
- Pre-merge code reviews for NestJS PRs
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

- **NestJS project roots** (`project.rootFolders`) — array of paths (relative to repo root) to NestJS application folders. Each is scanned independently and gets its own report section. Defaults to `["."]`.
- **Quality gate thresholds** (critical/high/medium limits) — applied per project; overall gate fails if any project fails.
- **Complexity limits** (cyclomatic, cognitive)
- **Test coverage minimum**
- **Ignored files/paths** (node_modules, dist/, etc.)
- **Custom rules** (team-specific patterns)
- **Severity overrides** (what constitutes critical vs. high)

Minimal monorepo example:

```json
{
  "project": {
    "rootFolders": [
      "apps/user-service",
      "apps/order-service",
      "apps/notification-service"
    ]
  },
  "qualityGate": {
    "maxCritical": 0,
    "maxHigh": 3,
    "maxMedium": 10
  },
  "output": {
    "suggestFixes": true
  }
}
```

See [review rules documentation](references/RULESET.md) for all review rules, [configuration guide](references/CONFIGURATION.md) for detailed setup instructions, and [input handling guide](references/INPUT_HANDLING.md) for branch/PR detection logic.
