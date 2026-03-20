# Next.js Review - Configuration Guide

Options for executing this skill are documented in the main [README.md](README.md). This guide covers configuration options once you've installed the skill.

---

## Configuration Setup

### 1. Copy Configuration Template

```bash
# Copy the default config to your project root
cp assets/.codereview.config.template.json .codereview.config.json
```

### 2. Customize for Your Team (Optional)

Edit `.codereview.config.json` to adjust:
```json
{
  "qualityGate": {
    "criticalIssuesAllowed": 0,      // Fail if any critical
    "highIssuesAllowed": 3,           // Adjust to your standards
    "minTestCoveragePercent": 70      // Require test coverage
  },
  "output": {
    "suggestFixes": true
  }
}
```

- `output.suggestFixes=true`: include fix suggestions when available.
- `output.suggestFixes=false`: hide fix suggestions.
- Line numbers are always included for every finding.

See [Configuration Guide](references/CONFIGURATION.md) for all options.

### 3. Select Team Profile

```bash
# Startup (Fast iteration)
cp assets/.codereview.startup.json .codereview.config.json

# Enterprise (Strict standards)
cp assets/.codereview.enterprise.json .codereview.config.json

# Fintech (Maximum security)
cp assets/.codereview.fintech.json .codereview.config.json
```

---

## How It Works

### Input Detection

The skill automatically detects input type:

| Input | Behavior |
|-------|----------|
| PR Link | Extracts source/destination from PR metadata |
| Branch Name + `--single` | Scans all files in that branch |
| Branch Name | Compares branch to default (main/master) |
| Invalid Input | Asks for clarification |

For detailed input handling logic, validation rules, and error scenarios, see [Input Handling Reference](references/INPUT_HANDLING.md).

### Review Output

Report is generated as `review-report.md` in your project root.
Sample report: See [Example Output](assets/SAMPLE_REPORT.md)

---

## Key Features

✅ **9 Review Dimensions**
- Next.js conventions & best practices
- Architecture & structure
- Complexity (cyclomatic, cognitive)
- Performance
- API routes & backend
- Database & data fetching
- Security
- Next.js & React best practices
- Testing & documentation

✅ **Severity Grouping**
- 🔴 Critical (security/crash risk)
- 🟠 High (architecture/performance)
- 🟡 Medium (maintainability)
- 🔵 Low (style/documentation)
- ⚪ Info (suggestions)

✅ **Quality Gate Decision**
- Automatic PASS/FAIL based on thresholds
- Configurable per team/project
- Detailed reasoning

✅ **Actionable Feedback**
- Why each issue matters
- How to fix it
- Impact prediction

---

## Troubleshooting

### CLI Not Found

```bash
# If copilot command not found
npm list -g @github/copilot

# Reinstall
npm install -g @github/copilot --force
```

### VSCode Extension Issues

1. Ensure Copilot Chat or Claude Code is installed from VS Code Extensions
2. Sign in with your GitHub/Copilot account
3. Try typing `@nextjs-review` in the chat

### Review Configuration Issues

1. Ensure `.codereview.*.json` files exist in project root or assets/
2. Verify JSON syntax is valid: `node -e "JSON.parse(require('fs').readFileSync('.codereview.config.json'))"`
3. Check that all required fields are present

---

## Understanding the Report

### Header Section
- PR details (link, files changed, diff stats)
- **Quality Gate Result** (✅ PASS or ❌ FAIL)

### Issues by Severity
- **Critical** (🔴) - Must fix before merge
- **High** (🟠) - Must fix before production
- **Medium** (🟡) - Fix in next sprint
- **Low** (🔵) - Nice to have
- **Info** (⚪) - Suggestions

### Analysis by Dimension
- Score for each of 9 review areas
- Summary of findings per area

### Recommendations
- **BEFORE MERGE**: Blockers
- **BEFORE PRODUCTION**: High priority
- **NEXT SPRINT**: Technical debt

---

## Next Steps

1. ✅ Copy config: `cp assets/.codereview.config.template.json .codereview.config.json`
2. ✅ Test it: Use Method 1 or 2 from [README.md](README.md)
3. ✅ Review report: See [SAMPLE_REPORT.md](assets/SAMPLE_REPORT.md)
4. ✅ Customize config for your team
5. ✅ Update team workflow

### Single-Branch Review Example

Use this mode when you want a full scan of one branch (not diff-only):

```bash
# Single branch full scan
/nextjs-review feature/auth-system --single
```

---

## More Information

- **Full Configuration Guide**: [Configuration Reference](references/CONFIGURATION.md)
- **Complete Ruleset**: [Review Rules](references/RULESET.md)
- **Skill Details**: [Skill Definition](SKILL.md)
- **Sample Report**: [Example Output](assets/SAMPLE_REPORT.md)

---

## Support

For issues or questions:
1. Check [Configuration Troubleshooting](references/CONFIGURATION.md#troubleshooting-configuration)
2. Review [Review Rules](references/RULESET.md) for specific rule details
3. See [Example Output](assets/SAMPLE_REPORT.md) for example output
