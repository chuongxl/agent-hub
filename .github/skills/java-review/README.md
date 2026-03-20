# Java Review Skill

Automated deep code reviews for Java pull requests and branches with 9 analysis dimensions, configurable quality gates, and severity-ranked issue reporting. Accept PR links or branch names with automatic source/destination detection.

## Quick Links

- **Get Started**: [QUICKSTART.md](QUICKSTART.md) — 5-minute setup guide
- **Main Skill Definition**: [SKILL.md](SKILL.md) — Complete workflow and instructions
- **Input Handling**: [references/INPUT_HANDLING.md](references/INPUT_HANDLING.md) — Branch/PR detection and validation
- **Review Rules**: [references/RULESET.md](references/RULESET.md) — All 100+ review rules
- **Configuration**: [references/CONFIGURATION.md](references/CONFIGURATION.md) — Config guide and team profiles
- **Sample Report**: [assets/SAMPLE_REPORT.md](assets/SAMPLE_REPORT.md) — Example output

## Features

### 9 Review Dimensions
1. Java conventions & best practices
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
- ✅ **Actionable Feedback** — Clear "why" and "how to fix" for every issue
- ✅ **Quality Gates** — Pass/fail with configurable thresholds
- ✅ **CI/CD Ready** — GitHub Actions, GitLab CI templates included

## Usage

The skill accepts **PR links, PR numbers, or branch names**. Use one of three methods below:

### Method 1: VSCode Extensions (Recommended)

#### Using Copilot Chat
```
Open Copilot Chat in VSCode (Cmd+Shift+I / Ctrl+Shift+I)

Type:
  "Use the java-review skill to review PR 427"
  or
  "@java-review check this branch: feature/auth-service"
```

#### Using Claude Code
```
Open Claude Code in VSCode

Type:
  "Review this PR with java-review skill"
  or
  "@java-review https://github.com/myorg/service/pull/427"
```

### Method 2: CLI (GitHub Copilot CLI)

**Launch the interactive CLI:**
```bash
copilot
```

**Then in the interactive prompt, describe your review:**
```
# Review by PR link
[Agent prompt]> Use the java-review skill to review https://github.com/myorg/repo/pull/427

# Review by branch
[Agent prompt]> Review the feature/auth-system branch with java-review

# Review with a specific profile
[Agent prompt]> Use java-review with the fintech profile to review PR 427
claude-code invoke java-review "feature/auth-service"
claude-code invoke java-review "https://github.com/myorg/service/pull/427"
```

The skill automatically:
- Detects if input is a PR link, PR number, or branch name
- Extracts source/destination branches from PR metadata
- Compares branch to default (main/master) for branch reviews
- Asks for clarification if input is invalid

## Compatibility

- **Claude Code** ✅
- **GitHub Copilot** ✅
- **Copilot Chat** ✅

## Getting Started

1. **Review the workflow**: See [SKILL.md](SKILL.md)
2. **Check examples**: See [assets/SAMPLE_REPORT.md](assets/SAMPLE_REPORT.md)
3. **Setup**: See [QUICKSTART.md](QUICKSTART.md)
4. **Configure**: See [references/CONFIGURATION.md](references/CONFIGURATION.md)

## Specification Compliance

- Name validation (java-review)
- agentskills.io specification compliance
- Progressive disclosure (metadata → skill → references)
- Proper file references (relative paths)
- SonarQube integration support
- Team profile configurations

See [COMPLIANCE.md](COMPLIANCE.md) for full specification validation.

## Support & Reference

- **Setup Issues**: See [QUICKSTART.md](QUICKSTART.md#troubleshooting)
- **Review Rules**: See [references/RULESET.md](references/RULESET.md)
- **Configuration**: See [references/CONFIGURATION.md](references/CONFIGURATION.md)
- **Input Handling**: See [references/INPUT_HANDLING.md](references/INPUT_HANDLING.md)
- **Validation**: See [VALIDATION_REPORT.md](VALIDATION_REPORT.md)
