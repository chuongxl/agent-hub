# Next.js Review Skill

Automated deep code reviews for Next.js pull requests and branches with 9 analysis dimensions, configurable quality gates, and severity-ranked issue reporting. Accept PR links or branch names with automatic source/destination detection.

## Quick Links

- **Get Started**: [QUICKSTART.md](QUICKSTART.md) — Configuration guide
- **Main Skill Definition**: [SKILL.md](SKILL.md) — Complete workflow and instructions
- **Input Handling**: [references/INPUT_HANDLING.md](references/INPUT_HANDLING.md) — Branch/PR detection and validation
- **Review Rules**: [references/RULESET.md](references/RULESET.md) — All 100+ review rules
- **Configuration**: [references/CONFIGURATION.md](references/CONFIGURATION.md) — Config guide and team profiles
- **Sample Report**: [assets/SAMPLE_REPORT.md](assets/SAMPLE_REPORT.md) — Example output

## Features

### 9 Review Dimensions
1. Next.js conventions & best practices
2. Code architecture & structure
3. Complexity analysis
4. Performance issues
5. API routes & backend
6. Database & data fetching
7. Security issues
8. Next.js & React best practices
9. Testing & documentation

### Key Benefits
- ✅ **Flexible Input** — PR links, PR numbers, or branch names
- ✅ **Automated Analysis** — Deep scanning across 9 dimensions
- ✅ **Configurable Standards** — Team profiles (Startup, Enterprise, Fintech)
- ✅ **Line-Level Findings** — Every issue includes exact file and line number in results and reports
- ✅ **Actionable Feedback** — Clear "why" and "how to fix" for every issue
- ✅ **Optional Fix Suggestions** — Suggest fixes when available, controlled by config (`output.suggestFixes`)
- ✅ **Quality Gates** — Pass/fail with configurable thresholds
- ✅ **CI/CD Ready** — GitHub Actions, GitLab CI templates included

## Usage

The skill accepts **PR links, PR numbers, or branch names** and supports single-branch scan mode (`--single`). Use one of three methods below:

### Method 1: VSCode Extensions (Recommended)

#### Using Copilot Chat
```
Open Copilot Chat in VSCode (Cmd+Shift+I / Ctrl+Shift+I)

Type:
  "Use the nextjs-review skill to review PR 427"
  or
  "@nextjs-review check this branch: feature/auth-system"
```

#### Using Claude Code
```
Open Claude Code in VSCode

Type:
  "Review this PR with nextjs-review skill"
  or
  "@nextjs-review https://github.com/myorg/repo/pull/427"
```

### Method 2: CLI (GitHub Copilot CLI)

**Launch the interactive CLI:**
```bash
copilot
```

**Then in the interactive prompt, describe your review:**
```
# Review by PR link
[Agent prompt]> Use the nextjs-review skill to review https://github.com/myorg/repo/pull/427

# Review by branch
[Agent prompt]> Review the feature/auth-system branch with nextjs-review

# Review a single branch (scan all files in that branch)
[Agent prompt]> Review feature/auth-system with nextjs-review --single

# Review with a specific profile
[Agent prompt]> Use nextjs-review with the fintech profile to review PR 427
```

### Method 3: CI/CD Pipeline

Use GitHub's native Copilot Code Review or mention @copilot in PR comments:

```
@copilot Use the nextjs-review skill to review this PR
```

The skill automatically:
- Detects if input is a PR link, PR number, or branch name
- Extracts source/destination branches from PR metadata
- For branch input with `--single`, scans all files in that branch
- For branch input without `--single`, compares branch to default (main/master)
- Asks for clarification if input is invalid
