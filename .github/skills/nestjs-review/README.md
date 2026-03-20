# NestJS Review Skill

Automated deep code reviews for NestJS pull requests and branches with 9 analysis dimensions, configurable quality gates, and severity-ranked issue reporting. Accept PR links or branch names with automatic source/destination detection.

## Quick Links

- **Get Started**: [QUICKSTART.md](QUICKSTART.md) — 5-minute setup guide
- **Main Skill Definition**: [SKILL.md](SKILL.md) — Complete workflow and instructions
- **Input Handling**: [references/INPUT_HANDLING.md](references/INPUT_HANDLING.md) — Branch/PR detection and validation
- **Review Rules**: [references/RULESET.md](references/RULESET.md) — All 100+ review rules
- **Configuration**: [references/CONFIGURATION.md](references/CONFIGURATION.md) — Config guide and team profiles
- **Sample Report**: [assets/SAMPLE_REPORT.md](assets/SAMPLE_REPORT.md) — Example output

## Features

### 9 Review Dimensions
1. NestJS conventions & best practices
2. Code architecture & structure
3. Complexity analysis
4. Performance issues
5. SQL & database queries
6. Database schema
7. Security issues
8. SonarQube-compatible rules
9. Error handling & observability

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
  "Use the nestjs-review skill to review PR 427"
  or
  "@nestjs-review check this branch: feature/auth-system"
```

#### Using Claude Code
```
Open Claude Code in VSCode

Type:
  "Review this PR with nestjs-review skill"
  or
  "@nestjs-review https://github.com/myorg/repo/pull/427"
```

### Method 2: CLI 

**Launch the interactive CLI:**
```bash
copilot or claude
```

**Then in the interactive prompt, describe your review:**
```
# Review by PR link
[Agent prompt]> Use the nestjs-review skill to review https://github.com/myorg/repo/pull/427

# Review by branch
[Agent prompt]> Review the feature/auth-system branch with nestjs-review

# Review a single branch (scan all files in that branch)
[Agent prompt]> Review feature/auth-system with nestjs-review --single

# Review with a specific profile
[Agent prompt]> Use nestjs-review with the fintech profile to review PR 427
claude -p "/nestjs-review "feature/auth-system"
claude -[] "/nestjs-review "https://github.com/myorg/repo/pull/427"
```

The skill automatically:
- Detects if input is a PR link, PR number, or branch name
- Extracts source/destination branches from PR metadata
- For branch input with `--single`, scans all files in that branch
- For branch input without `--single`, compares branch to default (main/master)
- Asks for clarification if input is invalid

## Compatibility

- **Claude Code** ✅
- **GitHub Copilot** ✅
- **Copilot Chat** ✅
- **Git-based workflows** (GitHub, GitLab, Bitbucket)

## Directory Structure

```
nestjs-review/
├── SKILL.md                     — Main skill definition
├── QUICKSTART.md                — Quick start guide
├── references/
│   ├── RULESET.md              — Complete review rules
│   └── CONFIGURATION.md         — Config guide + profiles
└── assets/
    ├── .codereview.config.template.json
    └── SAMPLE_REPORT.md         — Example review output
```

## File Size & Performance

| File | Lines | Purpose | Loading |
|------|-------|---------|---------|
| SKILL.md | 261 | Workflow + instructions | Initial activation |
| RULESET.md | 600+ | Rule definitions | On-demand (references) |
| CONFIGURATION.md | 500+ | Config guide | On-demand (references) |
| assets/ | — | Templates & samples | On-demand (assets) |

**Progressive Disclosure**: Metadata loaded first (~100 tokens), then SKILL.md on activation (~2000 tokens), then references/assets on-demand.

## Specification Compliance

✅ **agentskills.io Specification**
- YAML frontmatter with all required fields
- Name validation (nestjs-review)
- Description includes WHAT and WHEN
- Compatibility explicitly stated
- allowed-tools field specified
- Relative file references (one level deep)
- Main SKILL.md under 500 lines
- Progressive disclosure structure

✅ **Agent Compatibility**
- Claude Code support
- GitHub Copilot support
- Tool allowlist specified

## Next Steps

1. **Start Here**: [QUICKSTART.md](QUICKSTART.md)
2. **Review Examples**: [assets/SAMPLE_REPORT.md](assets/SAMPLE_REPORT.md)
3. **Customize**: [references/CONFIGURATION.md](references/CONFIGURATION.md)
4. **Integrate**: GitHub Actions / GitLab CI templates in QUICKSTART.md

## Questions?

- Review Rules: See [references/RULESET.md](references/RULESET.md)
- Setup Issues: See [QUICKSTART.md](QUICKSTART.md#troubleshooting)
- Configuration: See [references/CONFIGURATION.md](references/CONFIGURATION.md)
