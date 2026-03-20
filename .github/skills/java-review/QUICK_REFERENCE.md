# Java Review - Quick Reference ✅

**Status**: Production Ready  
**Date**: March 19, 2026

---

## 🎯 What is Java Review Skill

Automated deep code reviews for Java pull requests and branches using 9 review dimensions with automatic PR/branch detection.

```bash
# All of these work:
skill-invoke java-review "https://github.com/acme/service/pull/427"
skill-invoke java-review "pr:427"
skill-invoke java-review "feature/user-service"
```

---

## 📋 9 Review Dimensions

| # | Dimension | Focus | Severity |
|---|-----------|-------|----------|
| 1 | **Conventions** | Naming, formatting, organization | Medium |
| 2 | **Architecture** | Design patterns, SOLID, layering | High |
| 3 | **Complexity** | Cyclomatic, cognitive, method size | Medium |
| 4 | **Performance** | N+1 queries, memory, optimization | High |
| 5 | **Database** | SQL, queries, indexes, schema | High |
| 6 | **Security** | Injection, secrets, auth, crypto | Critical |
| 7 | **SonarQube** | Duplication, dead code, type safety | High |
| 8 | **Error Handling** | Exceptions, logging, resources | High |

---

## 🔄 Input Detection

```
PR Link              → Extract from GitHub/GitLab/Bitbucket API
PR Number (427)      → Use current repo context
Branch (feature/x)   → Compare to main/master default
Invalid Input        → Ask for clarification with suggestions
```

---

## ✨ Key Features

✅ **Flexible Input** — Accept PR links, PR numbers, or branch names  
✅ **Auto-Detection** — Identify input type and extract branches  
✅ **Smart Validation** — Helpful error messages with suggestions  
✅ **Configurable** — Startup/Enterprise/Fintech profiles  
✅ **CI/CD Ready** — GitHub Actions, GitLab CI templates  

---

## 📊 Report Structure

```
• Summary (files, issues, pass/fail)
• Issues by Severity (Critical → Info)
• Analysis by Dimension (9 areas)
• Quality Gate Decision (PASS/FAIL)
• Recommendations (priority fixes)
• Next Steps
```

---

## 🚀 Quick Start

**Step 1**: Copy config template
```bash
cp assets/.codereview.config.template.json .codereview.config.json
```

**Step 2**: Invoke skill with PR or branch
```bash
skill-invoke java-review "https://github.com/owner/repo/pull/427"
```

**Step 3**: Review report
```bash
cat review-report.md
```

---

## 📚 Documentation Map

- [**SKILL.md**](SKILL.md) — Main skill definition & workflow
- [**QUICKSTART.md**](QUICKSTART.md) — Setup & usage guide
- [**references/RULESET.md**](references/RULESET.md) — 100+ review rules
- [**references/CONFIGURATION.md**](references/CONFIGURATION.md) — Customize settings
- [**references/INPUT_HANDLING.md**](references/INPUT_HANDLING.md) — PR/branch detection
- [**assets/SAMPLE_REPORT.md**](assets/SAMPLE_REPORT.md) — Example output

---

## 🎯 Use Cases

✅ **Review before merge** — GitHub/GitLab/Bitbucket PRs  
✅ **Branch validation** — Check code before opening PR  
✅ **Security scans** — Detect SQL injection, hardcoded secrets  
✅ **Architecture checks** — SOLID principles, design patterns  
✅ **Performance audit** — N+1 queries, memory leaks  
✅ **Team standards** — Enforce code quality thresholds  

---

## ⚙️ Configuration Profiles

| Profile | Use Case | Critical | High | Medium |
|---------|----------|----------|------|--------|
| **Startup** | Fast iteration | 0 | 10 | 20 |
| **Enterprise** | Production | 0 | 1 | 3 |
| **Fintech** | Security-critical | 0 | 0 | 1 |

---

## 🔍 Example Review

### Input
```bash
$ skill-invoke java-review "feature/null-pointer-fix"
```

### Output
```
✅ Detected: Branch comparison
📊 Source: feature/null-pointer-fix → main
📁 Files: 4 changed, +156 lines, -42 lines
🔍 Analyzing across 9 dimensions...

📋 Results:
  🔴 0 Critical Issues
  🟠 1 High Issue (String concat in loop)
  🟡 3 Medium Issues
  ✅ PASS ← Within thresholds

📊 Top issues:
  1. [HIGH] String concatenation in performance loop
  2. [MEDIUM] Missing @Transactional on save method
  3. [MEDIUM] Unclosed ResultSet in query
```

---

## 🛠️ Common Commands

```bash
# Review PR
skill-invoke java-review "pr:427"

# Review branch
skill-invoke java-review "feature/auth-service"

# With custom config
skill-invoke java-review "feature/x" --config .codereview.enterprise.json

# Validate config
skill-invoke java-review --validate-config
```

---

## 📞 Troubleshooting

| Problem | Solution |
|---------|----------|
| PR not found | Check PR link format or number |
| Branch not found | List branches: `git branch -r` |
| Can't parse input | Provide full PR URL or clear branch name |
| Rules not triggering | Ensure rule is enabled in config |
| Too strict | Increase allowed issue thresholds |

---

## ✅ Skill Status

- **agentskills.io Compliance**: ✅ Full
- **Claude Code**: ✅ Compatible
- **GitHub Copilot**: ✅ Compatible
- **Production Ready**: ✅ Yes

See [COMPLIANCE.md](COMPLIANCE.md) for full specification validation.
