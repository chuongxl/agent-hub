---
name: java-review
description: Conduct automated deep code reviews for Java pull requests or branches by analyzing code conventions, architecture, complexity, performance, SQL/database queries, security issues, and SonarQube findings. Accepts PR links or branch names with automatic source/destination detection. Generates comprehensive severity-ranked review reports with quality gate pass/fail decisions. Use when reviewing Java PRs before merge, comparing branches, evaluating code quality, ensuring security compliance, or managing technical debt.
license: MIT
compatibility: Requires git, Java/Maven or Gradle, and network access. Works with GitHub, GitLab, and Bitbucket PR URLs. Compatible with Claude Code and GitHub Copilot.
metadata:
  author: self-learning-suite
  version: "1.0"
  category: code-review
  framework: java
  difficulty: advanced
  estimated-duration: "20-35 minutes per review"
  activation-keywords: "code review, PR review, branch review, Java review, review rules, check PR, check branch, compare branch, audit code, quality gate, Java analysis"
  supported-agents: ["Claude Code", "GitHub Copilot", "Copilot Chat"]
allowed-tools: Read Bash(git:clone,diff,log,status,checkout) Bash(mvn:install,test) Bash(java:exec)
---

# Java Code Review Skill

## Overview

Automate deep code reviews for Java pull requests across 9 critical dimensions: conventions, architecture, complexity, performance, database queries, security, SonarQube issues, and more. Generate scored review reports with severity-based grouping and quality gate decisions.

## Core Workflow

This skill follows a 5-phase review workflow:

1. **Input Capture & Branch Setup** — Accept PR link or branch name, validate input, and setup comparison branches
2. **Multi-Dimensional Analysis** — Scan 9 review areas simultaneously (conventions, architecture, performance, etc.)
3. **Issue Collection & Scoring** — Categorize findings by severity (Critical, High, Medium, Low, Info)
4. **Report Generation** — Create structured report with issues ranked by severity and impact
5. **Quality Gate Decision** — Determine pass/fail based on configurable thresholds

**Typical Duration**: 20-35 minutes depending on code changes size

## Step-by-Step Instructions

### Phase 1: Input Capture & Branch Setup

#### Step 1a: Capture User Input
- Ask user: "Provide a PR link, PR number, or branch name to review"
- Examples accepted:
  - `https://github.com/owner/repo/pull/123`
  - `https://gitlab.com/owner/repo/-/merge_requests/456`
  - `https://bitbucket.org/owner/repo/pull-requests/789`
  - PR number: `123` (requires repo context)
  - Branch name: `feature/user-service`

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

#### Step 1d: Setup Review Environment
- Clone repository (or update existing clone): `git clone <repo_url>` or `git pull`
- Fetch all branches: `git fetch --all`
- Switch to source branch: `git checkout <source_branch>`
- Identify changed files: `git diff --name-only <destination_branch>...<source_branch>`
- Count changes: `git diff --stat <destination_branch>...<source_branch>`
- Scan for pom.xml or build.gradle to confirm Java project
- Present scope summary:
  ```
  Review Type: [PR / Branch Comparison]
  Source Branch: feature/user-service
  Destination Branch: main
  Files Changed: X files
  Lines: Y added, Z deleted
  ```

### Phase 2: Multi-Dimensional Analysis

Analyze the code across 9 dimensions in parallel:

#### 1. **Java Conventions & Best Practices**
- Naming conventions (PascalCase for classes, camelCase for methods/variables)
- Code formatting and style (indentation, spacing, line length)
- JavaDoc comments on public APIs
- Package structure and organization
- Proper use of access modifiers (public/private/protected)
- Generics usage and type safety
- Exception handling patterns
- Logging conventions

#### 2. **Code Architecture & Structure**
- Design patterns (Factory, Strategy, Observer, Builder, etc.)
- SOLID principles compliance
- Separation of concerns (controllers, services, repositories, utilities)
- Dependency injection and loose coupling
- Appropriate abstraction levels
- Interface usage and implementation
- Layer boundaries (presentation → business → data)

#### 3. **Complexity Issues**
- Cyclomatic complexity per method (flag >10)
- Method length (flag methods >50 lines)
- Cognitive complexity (nested conditions, loops >15)
- Class complexity (flag >20 public methods)
- Nesting depth (flag >4 levels)
- Parameter count per method (flag >5 parameters)
- Constructor complexity

#### 4. **Performance Issues**
- N+1 query patterns (eager loading missing)
- Inefficient string concatenation (use StringBuilder)
- Unnecessary object creation in loops
- Missing caching mechanisms
- Synchronization bottlenecks
- Inefficient synchronous I/O operations
- Memory leak risks (unclosed resources)
- Collection sizing issues (initial capacity)

#### 5. **SQL & Database Query Issues**
- N+1 query problems (missing eager loading or batch queries)
- Missing database indexes on FK relationships
- Inefficient joins (multiple queries instead of single join)
- Missing query optimization (select specific columns)
- Transaction handling issues
- Prepared statement usage (SQL injection prevention)
- Pagination missing on large result sets
- Cursor/ResultSet resource management

#### 6. **Database Schema Issues**
- Missing constraints (NOT NULL, UNIQUE, FK)
- Poor naming conventions for columns/tables
- Missing indexes on frequently queried columns
- Denormalization without justification
- Data type mismatches
- Migration file issues
- Missing primary keys
- Cascade delete configurations

#### 7. **Security Issues**
- Missing input validation/sanitization
- Hardcoded secrets/credentials (API keys, passwords)
- Insufficient authorization checks (role-based access)
- Missing authentication on sensitive endpoints
- SQL injection vulnerabilities
- Cross-site scripting (XSS) vulnerabilities
- Insecure cryptography usage
- Unsafe deserialization (ObjectInputStream)
- Path traversal vulnerabilities
- Missing CSRF protection
- Unsafe file uploads

#### 8. **SonarQube-Compatible Issues**
- Code duplications (flag >3% duplication)
- Dead code (unused imports, variables, methods)
- Type safety issues (raw types, unchecked casts)
- Cognitive complexity violations
- Test coverage gaps (flag untested critical paths)
- Security hotspots (crypto usage, file operations, command execution)
- Code smell issues (magic numbers, long methods)
- Null pointer dereference risks

#### 9. **Additional Issues**
- Error handling (missing try-catch, silent failures, swallowing exceptions)
- Logging coverage (missing business-critical logs)
- JavaDoc gaps (missing public API documentation)
- Testing gaps (missing unit/integration tests)
- Dependency vulnerabilities (outdated packages)
- Thread safety issues (mutable shared state)
- Resource management (proper close/cleanup)

### Phase 3: Issue Collection & Scoring

For each issue found, record:
- **Issue ID**: Unique identifier
- **Area**: Which of 9 dimensions (conventions, architecture, etc.)
- **Severity**: Critical | High | Medium | Low | Info
- **File & Line**: Location in code
- **Description**: What's wrong
- **Impact**: Why it matters
- **Suggestion**: How to fix

**Severity Levels**:
- **Critical**: Security vulnerability, production crash risk, data loss risk
- **High**: Performance degradation, architecture violation, major bug potential
- **Medium**: Code quality, maintainability, minor performance concern
- **Low**: Style, minor naming issue, documentation gap
- **Info**: Suggestion for improvement, FYI comment

### Phase 4: Report Generation

Generate structured report with sections:

```markdown
# 📋 Java Code Review Report

## Summary
- PR: [PR Link]
- Repository: [Repo]
- Files Changed: [X files, Y additions, Z deletions]
- Issues Found: [Total count by severity]
- Quality Gate: [PASS/FAIL]

## Issues by Severity

### 🔴 Critical Issues (X found)
1. [Issue 1] - [File:Line]
   - Description: ...
   - Impact: ...
   - Fix: ...

### 🟠 High Issues (X found)
[Similar format]

### 🟡 Medium Issues (X found)
[Similar format]

### 🔵 Low Issues (X found)
[Similar format]

### ⚪ Info (X found)
[Similar format]

## Analysis by Dimension

### 1. Java Conventions & Best Practices
- Status: [✓ Good / ⚠️ Issues / ✗ Critical]
- Findings: [X issues, brief summary]

### 2. Code Architecture & Structure
[Similar format for all 9 dimensions]

... (continue for all 9 areas)

## Quality Gate

**Overall Score**: [X/100]

**Threshold Configuration**:
- Critical Issues Allowed: [X (default 0)]
- High Issues Allowed: [X (default 3)]
- Medium Issues Allowed: [X (default 10)]
- Average Complexity: [< 10 (flag if higher)]

**Decision**: [✅ PASS / ❌ FAIL]

**Reasoning**: [Why pass/fail based on thresholds]

## Recommendations

1. [Priority 1 fix]
2. [Priority 2 fix]
3. [Priority 3 fix]
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

---

## Key Principles

- **Deep Analysis**: Don't just flag style issues—catch architectural and security problems
- **Actionable**: Every issue includes "why" and "how to fix"
- **Fast**: Complete reviews in 20-35 min for typical PRs (< 500 lines changed)
- **Transparent**: Show thresholds and reasoning for pass/fail
- **Team-Friendly**: Issues ranked by impact, easy to prioritize fixes

## When to Use This Skill

✅ **Use for**:
- Pre-merge code reviews for Java PRs
- Automated architecture compliance checks
- Security scanning before production deploys
- Technical debt identification
- Team code quality standards enforcement

❌ **Don't use for**:
- Functional testing (use CI/CD test suites)
- Business logic validation (owner's responsibility)
- Manual architectural design decisions (needs human discussion)

## Configuration Options

Customize per project/team:
- **Quality gate thresholds** (critical/high/medium limits)
- **Complexity limits** (cyclomatic, cognitive)
- **Test coverage minimum**
- **Ignored files/paths** (node_modules, target/, dist/, etc.)
- **Custom rules** (team-specific patterns)
- **Severity overrides** (what constitutes critical vs. high)

See [review rules documentation](references/RULESET.md) for all review rules, [configuration guide](references/CONFIGURATION.md) for detailed setup instructions, and [input handling guide](references/INPUT_HANDLING.md) for branch/PR detection logic.
