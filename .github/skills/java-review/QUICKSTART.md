# Java Review - Configuration Guide

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
| Branch Name | Compares branch to default (main/master) |
| Invalid Input | Asks for clarification |

For detailed input handling logic, validation rules, and error scenarios, see [Input Handling Reference](references/INPUT_HANDLING.md).

### Review Output

Report is generated as `review-report.md` in your project root.
Sample report: See [Example Output](assets/SAMPLE_REPORT.md)

---

## Troubleshooting


### VSCode Extension Issues

1. Ensure Copilot Chat or Claude Code is installed from VS Code Extensions
2. Sign in with your GitHub/Copilot account
3. Try typing `@java-review` in the chat

### CI/CD Script Fails

1. Ensure CLI is installed in build environment
2. Check that `.codereview.*.json` files exist
3. Verify git history is available (use `fetch-depth: 0` in GitHub Actions)
4. Check build logs for specific error messages

---

## Key Features

✅ **9 Review Dimensions**
- Java conventions & best practices
- Code architecture & structure
- Complexity analysis
- Performance issues
- SQL & database queries
- Database schema
- Security issues
- SonarQube-compatible rules
- Error handling & observability

✅ **Flexible Input Types**
- GitHub, GitLab, Bitbucket PR links
- Direct PR numbers
- Branch names
- Invalid input with helpful suggestions

✅ **Quality Gates**
- Configurable severity thresholds
- Automatic pass/fail decisions
- Detailed reasoning in reports

✅ **Multiple Team Profiles**
- **Startup**: Lenient (fast iteration)
- **Enterprise**: Strict (quality-focused)
- **Fintech**: Maximum security

---

## Example Scenarios

### Scenario 1: Review Before Merge
```bash
$ /java-review "https://github.com/company/api/pull/156"

✅ Detected: GitHub PR #156
📊 Source: feature/user-auth → Destination: main
📁 Files changed: 12 files, +342 lines, -89 lines
🔍 Starting 9-dimensional analysis...

[Analysis output...]

📋 Results:
  🔴 0 Critical Issues
  🟠 2 High Issues (within 3-issue limit)
  🟡 8 Medium Issues (within 10-issue limit)
  ✅ PASS
```

### Scenario 2: Review Local Branch Before Opening PR
```bash
$ /java-review "feature/performance-optimization"

✅ Detected: Branch (local/remote)
📊 Comparing: feature/performance-optimization → main
📁 Files changed: 8 files, +156 lines, -42 lines
🔍 Starting 9-dimensional analysis...

[Analysis output...]

📋 Results:
  🔴 0 Critical Issues
  🟠 1 High Issue
  🟡 5 Medium Issues
  ✅ PASS
```

### Scenario 3: Handle Invalid Input Gracefully
```bash
$ /java-review "auth"

❌ Multiple branches match 'auth':
   • feature/auth-service
   • feature/auth-redesign
   • hotfix/auth-bug

Please be more specific OR provide full PR link

Retrying: skill-invoke java-review "feature/auth-service"
✅ Detected: Branch
```

---

## Troubleshooting

### Problem: Cannot Clone Repository
```
Error: Repository not accessible
```

**Solutions**:
1. Check network connection
2. Verify PR link format
3. If private repo, ensure credentials configured

### Problem: Build Fails During Review
```
Error: Cannot compile/analyze code
```

**Solutions**:
1. Ensure pom.xml or build.gradle present
2. Check Java version compatibility
3. Review compilation errors: `mvn clean compile`

### Problem: No Rules Matching Code
```
Warning: No violations found (unexpected)
```

**Solutions**:
1. Review configuration: `.codereview.config.json`
2. Check if rules apply to your codebase
3. See [Configuration Troubleshooting](references/CONFIGURATION.md#troubleshooting-configuration)

### Problem: Report Not Generated
```
Error: review-report.md not found
```

**Solutions**:
1. Check skill execution completed
2. Verify write permissions to current directory
3. Check if any Critical errors caused early exit

---

## Advanced Configuration

### Custom Rule Severity
```json
{
  "rules": {
    "MissingJavaDoc": {
      "severity": "Medium",  // Override default
      "enabled": true
    }
  }
}
```

See [Configuration Guide](references/CONFIGURATION.md) for all options.

---

## Key Resources

- **Full Skill Definition**: [SKILL.md](SKILL.md)
- **Configuration Guide**: [references/CONFIGURATION.md](references/CONFIGURATION.md)
- **Complete Rule Set**: [references/RULESET.md](references/RULESET.md)
- **Input Handling Details**: [references/INPUT_HANDLING.md](references/INPUT_HANDLING.md)
- **Sample Report**: [assets/SAMPLE_REPORT.md](assets/SAMPLE_REPORT.md)

---

## Summary

| Step | Action | Time |
|------|--------|------|
| 1 | Copy config template | 1 min |
| 2 | Invoke skill | 1 min |
| 3 | Provide PR link or branch | 1 min |
| 4 | Wait for analysis | 20-35 min |
| 5 | Review report | 2 min |
| **Total** | **Complete Review** | **25-40 min** |
