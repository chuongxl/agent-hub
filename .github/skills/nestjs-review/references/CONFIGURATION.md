# NestJS Code Review - Configuration Guide

Configure the code review skill per team, project, or environment.

---

## Configuration File

Create `.codereview.config.json` in your project root:

```json
{
  "qualityGate": {
    "criticalIssuesAllowed": 0,
    "highIssuesAllowed": 3,
    "mediumIssuesAllowed": 10,
    "lowIssuesAllowed": 20,
    "maxAverageCyclomaticComplexity": 10,
    "maxAverageCognitiveComplexity": 15,
    "minTestCoveragePercent": 70,
    "maxDuplicationPercent": 3
  },
  "rules": {
    "enabled": [
      "nestjs-conventions",
      "code-architecture",
      "complexity-analysis",
      "performance-issues",
      "database-queries",
      "database-schema",
      "security-issues",
      "sonarqube-issues",
      "error-handling"
    ],
    "severity-overrides": {
      "missing-injectable": "critical",
      "unused-import": "low",
      "business-logic-in-controller": "critical"
    },
    "ignored-patterns": [
      "**/node_modules/**",
      "**/dist/**",
      "**/*.spec.ts",
      "**/*.e2e.spec.ts"
    ]
  },
  "complexity": {
    "maxCyclomaticComplexity": 10,
    "maxCognitiveComplexity": 15,
    "maxMethodLength": 50,
    "maxNestingDepth": 4,
    "maxParameterCount": 4,
    "maxClassMethodCount": 20
  },
  "testing": {
    "minCoveragePercent": 70,
    "requiredTestFiles": [
      "**/*.service.ts",
      "**/*.controller.ts"
    ],
    "ignoredFiles": [
      "**/*.module.ts",
      "**/*.dto.ts"
    ]
  },
  "security": {
    "blockCriticalSecurityIssues": true,
    "requiredSecurityRules": [
      "hardcoded-secret",
      "sql-injection",
      "authentication-bypass",
      "missing-input-validation"
    ],
    "allowedCrypto": ["bcrypt", "argon2", "scrypt"],
    "forbiddenLibraries": ["md5", "crypto-js"]
  },
  "performance": {
    "maxQueryTimeMs": 100,
    "flagNoQueryLimit": true,
    "flagNoPageination": true,
    "maxDataTransferMB": 1
  },
  "database": {
    "requireTypeORM": true,
    "requireEagerLoading": false,
    "checkConstraints": true,
    "checkIndexes": true
  },
  "output": {
    "format": "markdown",
    "severityColors": {
      "critical": "🔴",
      "high": "🟠",
      "medium": "🟡",
      "low": "🔵",
      "info": "⚪"
    },
    "groupByDimension": true,
    "includeSummary": true,
    "includeRecommendations": true,
    "suggestFixes": true
  }
}
```

---

## Configuration Options Explained

### Quality Gate Thresholds

```json
"qualityGate": {
  "criticalIssuesAllowed": 0,        // Max critical issues (0 = fail if any found)
  "highIssuesAllowed": 3,             // Max high severity issues
  "mediumIssuesAllowed": 10,          // Max medium severity issues
  "lowIssuesAllowed": 20,             // Max low severity issues
  "maxAverageCyclomaticComplexity": 10,  // Avg across all methods
  "maxAverageCognitiveComplexity": 15,
  "minTestCoveragePercent": 70,       // Min code coverage required
  "maxDuplicationPercent": 3          // Max code duplication allowed
}
```

**Guidelines**:
- **Strict (enterprise)**: Critical=0, High=0, Medium=5, Coverage=80%
- **Moderate (startups)**: Critical=0, High=3, Medium=10, Coverage=70%
- **Relaxed (prototypes)**: Critical=0, High=5, Medium=20, Coverage=50%

### Complexity Thresholds

```json
"complexity": {
  "maxCyclomaticComplexity": 10,     // Max branches in single method
  "maxCognitiveComplexity": 15,      // Max mental effort score
  "maxMethodLength": 50,              // Max lines in method
  "maxNestingDepth": 4,               // Max nesting levels
  "maxParameterCount": 4,             // Max function parameters
  "maxClassMethodCount": 20           // Max public methods per class
}
```

**Rules of thumb**:
- Cyclomatic complexity > 10 = hard to test
- Method > 50 lines = likely doing too much
- Nesting > 4 = probably needs refactoring

### Security Configuration

```json
"security": {
  "blockCriticalSecurityIssues": true,     // Fail PR if critical security issue
  "requiredSecurityRules": [
    "hardcoded-secret",
    "sql-injection",
    "authentication-bypass"
  ],
  "allowedCrypto": ["bcrypt", "argon2"],   // Whitelist safe crypto libs
  "forbiddenLibraries": ["md5"]             // Blacklist unsafe libs
}
```

### Testing Configuration

```json
"testing": {
  "minCoveragePercent": 70,
  "requiredTestFiles": [
    "**/*.service.ts",      // All services must be tested
    "**/*.controller.ts"    // All controllers must be tested
  ],
  "ignoredFiles": [
    "**/*.module.ts",       // Don't require tests for modules
    "**/*.dto.ts"           // Don't require tests for DTOs
  ]
}
```

### Performance Configuration

```json
"performance": {
  "maxQueryTimeMs": 100,        // Flag queries slower than 100ms
  "flagNoQueryLimit": true,     // Flag SELECT * without LIMIT
  "flagNoPageination": true,    // Flag list endpoints without pagination
  "maxDataTransferMB": 1        // Flag responses > 1MB
}
```

### Output Configuration

```json
"output": {
  "format": "markdown",
  "groupByDimension": true,
  "includeSummary": true,
  "includeRecommendations": true,
  "suggestFixes": true
}
```

- `suggestFixes: true` => Include fix suggestions in result output and report when a reliable fix is available.
- `suggestFixes: false` => Omit fix suggestions.
- Line numbers are mandatory for all issues and always included in result output and report.

### Database Configuration

```json
"database": {
  "requireTypeORM": true,         // Enforce TypeORM usage
  "requireEagerLoading": false,   // Enforce eager loading (strict)
  "checkConstraints": true,        // Check for missing FK/UNIQUE
  "checkIndexes": true             // Check for missing indexes
}
```

---

## Team-Specific Profiles

### Profile: Startup (Fast iteration)
```json
{
  "qualityGate": {
    "criticalIssuesAllowed": 0,
    "highIssuesAllowed": 5,
    "mediumIssuesAllowed": 20
  },
  "security": {
    "blockCriticalSecurityIssues": true
  },
  "testing": {
    "minCoveragePercent": 50
  }
}
```

### Profile: Enterprise (Strict standards)
```json
{
  "qualityGate": {
    "criticalIssuesAllowed": 0,
    "highIssuesAllowed": 0,
    "mediumIssuesAllowed": 5
  },
  "security": {
    "blockCriticalSecurityIssues": true
  },
  "testing": {
    "minCoveragePercent": 80
  },
  "complexity": {
    "maxCyclomaticComplexity": 8,
    "maxMethodLength": 40
  }
}
```

### Profile: Fintech (Maximum security + stability)
```json
{
  "qualityGate": {
    "criticalIssuesAllowed": 0,
    "highIssuesAllowed": 0,
    "mediumIssuesAllowed": 3,
    "minTestCoveragePercent": 85
  },
  "security": {
    "blockCriticalSecurityIssues": true,
    "requiredSecurityRules": [
      "hardcoded-secret",
      "sql-injection",
      "authentication-bypass",
      "authorization-bypass",
      "missing-input-validation",
      "unsafe-file-upload"
    ]
  },
  "database": {
    "checkConstraints": true,
    "checkIndexes": true,
    "requireEagerLoading": true
  }
}
```

---

## Environment Variables

Override config with environment variables:

```bash
# Quality Gate
export REVIEW_CRITICAL_ALLOWED=0
export REVIEW_HIGH_ALLOWED=3
export REVIEW_MEDIUM_ALLOWED=10
export REVIEW_TEST_COVERAGE=70

# Complexity
export REVIEW_MAX_COMPLEXITY=10
export REVIEW_MAX_METHOD_LENGTH=50

# Security
export REVIEW_BLOCK_CRITICAL=true

# Git
export GITHUB_TOKEN=ghp_xxx
export GIT_BRANCH_COMPARE=main  # Compare against this branch
```

---

## Per-File Overrides

Add comments in code to override rules:

```typescript
// codeview-ignore: n-plus-one-query
// Reason: Performance premature optimization; fixing later
for (const user of users) {
  user.orders = await findOrders(user.id);
}

// codeview-severity: high -> low
// Reason: This is intentional and documented
const API_KEY = process.env.API_KEY;
```

---

## Pre-configured Templates

### `.codereview.config.json` (Basic)
```json
{
  "qualityGate": {
    "criticalIssuesAllowed": 0,
    "highIssuesAllowed": 3,
    "mediumIssuesAllowed": 10,
    "minTestCoveragePercent": 70
  },
  "rules": {
    "enabled": [
      "nestjs-conventions",
      "code-architecture",
      "complexity-analysis",
      "security-issues"
    ]
  }
}
```

### `.codereview.config.json` (Comprehensive)
```json
{
  "qualityGate": {
    "criticalIssuesAllowed": 0,
    "highIssuesAllowed": 2,
    "mediumIssuesAllowed": 8,
    "maxAverageCyclomaticComplexity": 9,
    "minTestCoveragePercent": 75
  },
  "rules": {
    "enabled": [
      "nestjs-conventions",
      "code-architecture",
      "complexity-analysis",
      "performance-issues",
      "database-queries",
      "database-schema",
      "security-issues",
      "sonarqube-issues",
      "error-handling"
    ]
  },
  "complexity": {
    "maxCyclomaticComplexity": 10,
    "maxMethodLength": 50,
    "maxNestingDepth": 4
  },
  "security": {
    "blockCriticalSecurityIssues": true
  },
  "output": {
    "format": "markdown",
    "groupByDimension": true
  }
}
```

---

## CI/CD Integration

### GitHub Actions

```yaml
name: NestJS Code Review

on: [pull_request]

jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Run Code Review
        run: |
          npm run code-review --pr=${{ github.event.pull_request.number }}
      - name: Comment on PR
        run: |
          cat review-report.md | gh pr comment ${{ github.event.pull_request.number }} -F -
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      - name: Fail if quality gate fails
        run: |
          grep "❌ FAIL" review-report.md && exit 1 || exit 0
```

### GitLab CI

```yaml
code_review:
  stage: review
  script:
    - npm run code-review --branch=$CI_COMMIT_REF_NAME
    - cat review-report.md
  artifacts:
    reports:
      dotenv: review-result.env
  allow_failure: false
```

---

## Troubleshooting Configuration

### Issue: Too many false positives
**Solution**: Adjust severity overrides or disable specific rules
```json
"rules": {
  "severity-overrides": {
    "unused-import": "info"  // Downgrade to info instead of medium
  }
}
```

### Issue: Quality gate too strict
**Solution**: Increase thresholds gradually
```json
"qualityGate": {
  "highIssuesAllowed": 5,      // Was 3
  "mediumIssuesAllowed": 15    // Was 10
}
```

### Issue: Too slow on large PRs
**Solution**: Exclude patterns
```json
"rules": {
  "ignored-patterns": [
    "**/generated/**",
    "**/migrations/**",
    "**/*.d.ts"
  ]
}
```

