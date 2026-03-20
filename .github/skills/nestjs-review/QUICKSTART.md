# NestJS Code Review - Configuration Guide

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
  }
}
```

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
- NestJS conventions & best practices
- Architecture & structure
- Complexity (cyclomatic, cognitive)
- Performance
- SQL & database queries
- Database schema
- Security
- SonarQube-compatible rules
- Error handling & observability

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


### VSCode Extension Issues

1. Ensure Copilot Chat or Claude Code is installed from VS Code Extensions
2. Sign in with your GitHub/Copilot account
3. Try typing `@nestjs-review` in the chat

### CI/CD Script Fails

1. Ensure CLI is installed in build environment
2. Check that `.codereview.*.json` files exist
3. Verify git history is available (use `fetch-depth: 0` in GitHub Actions)
4. Check build logs for specific error messages
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

## Troubleshooting

### "Git clone failed"
```bash
# Ensure git is installed and you have network access
git --version

# Check GitHub token if private repo
export GITHUB_TOKEN=ghp_xxx
```

### "Too many false positives"
1. Edit `.codereview.config.json`
2. Add to ignored patterns:
   ```json
   "ignored-patterns": ["**/generated/**"]
   ```
3. Or adjust severity:
   ```json
   "severity-overrides": {
     "unused-import": "info"
   }
   ```

### "Takes too long"
1. Review fewer dimensions (edit `rules.enabled` list)
2. Exclude patterns (edit `ignored-patterns`)
3. Increase max file size limit

---

## Next Steps

1. ✅ Copy config: `cp assets/.codereview.config.template.json .codereview.config.json`
2. ✅ Test it: `npm run code-review --pr=<your-pr>`
3. ✅ Review report: See [SAMPLE_REPORT.md](assets/SAMPLE_REPORT.md)
4. ✅ Customize config for your team
5. ✅ Integrate into CI/CD
6. ✅ Update team workflow

### Single-Branch Review Example

Use this mode when you want a full scan of one branch (not diff-only):

```bash
# Single branch full scan
/nestjs-review feature/auth-system --single
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

