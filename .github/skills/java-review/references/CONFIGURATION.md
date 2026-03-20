# Java Review Configuration Guide

Complete reference for configuring Java code review settings per team, project, and environment.

---

## Quick Start Configuration

Default configuration file: `.codereview.config.json`

```json
{
  "projectType": "java",
  "buildTool": "maven",  // or "gradle"
  "qualityGate": {
    "enabled": true,
    "criticalIssuesAllowed": 0,
    "highIssuesAllowed": 3,
    "mediumIssuesAllowed": 10,
    "lowIssuesAllowed": 50,
    "minTestCoveragePercent": 70
  },
  "analysis": {
    "dimensions": ["conventions", "architecture", "complexity", "performance", "database", "security", "sonarqube", "errorhandling"],
    "severityLevels": ["Critical", "High", "Medium", "Low", "Info"]
  }
}
```

---

## Configuration Options

### Project Settings

```json
{
  "projectType": "java",              // Required: java
  "projectName": "my-service",        // Optional: displayed in report
  "buildTool": "maven",               // "maven" or "gradle"
  "javaVersion": "11",                // Minimum Java version to check
  "enforceJavaVersion": false         // Fail if code uses unsupported syntax
}
```

### Quality Gate Configuration

```json
{
  "qualityGate": {
    "enabled": true,                  // Enable/disable quality gate
    "failOnCritical": true,          // Always fail if Critical found
    "criticalIssuesAllowed": 0,      // Max Critical issues (0 = none)
    "highIssuesAllowed": 3,          // Max High issues (default 3)
    "mediumIssuesAllowed": 10,       // Max Medium issues (default 10)
    "lowIssuesAllowed": 50,          // Max Low issues (default 50)
    "minTestCoveragePercent": 70,    // Min test coverage % (0-100)
    "enforceCoverage": false         // Fail if coverage below threshold
  }
}
```

### Analysis Dimensions

Enable/disable specific review dimensions:

```json
{
  "analysis": {
    "dimensions": {
      "conventions": {
        "enabled": true,
        "severity": "Medium"
      },
      "architecture": {
        "enabled": true,
        "severity": "High"
      },
      "complexity": {
        "enabled": true,
        "severity": "Medium"
      },
      "performance": {
        "enabled": true,
        "severity": "High"
      },
      "database": {
        "enabled": true,
        "severity": "High"
      },
      "security": {
        "enabled": true,
        "severity": "Critical"
      },
      "sonarqube": {
        "enabled": true,
        "severity": "High"
      },
      "errorhandling": {
        "enabled": true,
        "severity": "High"
      }
    }
  }
}
```

### Rule Configuration

Customize individual rules:

```json
{
  "rules": {
    "PublicAPIJavaDoc": {
      "enabled": true,
      "severity": "Medium",
      "excludePatterns": [
        "**/test/**",
        "**/generated/**"
      ]
    },
    "CyclomaticComplexity": {
      "enabled": true,
      "severity": "High",
      "threshold": 10,
      "reportAll": false          // Only flag violations
    },
    "MethodLength": {
      "enabled": true,
      "severity": "Medium",
      "maxLines": 50
    },
    "StringConcatenationInLoop": {
      "enabled": true,
      "severity": "High"
    },
    "SQLInjectionRisk": {
      "enabled": true,
      "severity": "Critical"
    },
    "HardcodedSecrets": {
      "enabled": true,
      "severity": "Critical",
      "patterns": [
        "password",
        "secret",
        "apikey",
        "token"
      ]
    }
  }
}
```

### Exclusions & Ignore Patterns

```json
{
  "exclusions": {
    "files": [
      "**/test/**",
      "**/tests/**",
      "**/*Test.java",
      "**/generated/**",
      "target/**",
      "build/**"
    ],
    "directories": [
      "src/test/java",
      "src/integration-test/java"
    ],
    "patterns": [
      ".*\\$\\d.*",           // Inner classes
      ".*Generated.*"         // Generated classes
    ]
  },
  "alwaysReviewThese": [
    "src/main/java/**/*",
    "!src/main/java/**/model/**"
  ]
}
```

### Complexity Thresholds

```json
{
  "complexity": {
    "cyclomaticComplexity": {
      "threshold": 10,
      "severity": "High"
    },
    "cognitivecomplexity": {
      "threshold": 15,
      "severity": "High"
    },
    "nppComplexity": {
      "threshold": 4,
      "severity": "Medium"
    },
    "methodLength": {
      "maxLines": 50,
      "severity": "Medium"
    },
    "classSize": {
      "maxLines": 500,
      "severity": "Medium"
    },
    "parameterCount": {
      "maximum": 5,
      "severity": "Medium"
    }
  }
}
```

### Database Configuration

```json
{
  "database": {
    "checkNPlus1": true,
    "checkMissingEagerLoading": true,
    "checkSelectStar": true,
    "checkMissingIndexes": true,
    "checkPreparedStatements": true,
    "dbType": "postgresql"          // postgresql, mysql, oracle, etc.
  }
}
```

### Security Configuration

```json
{
  "security": {
    "checkHardcodedSecrets": true,
    "checkSQLInjection": true,
    "checkXSS": true,
    "checkAuthenticationBypass": true,
    "checkWeakCrypto": true,
    "checkInsecureDeserialization": true,
    "secretPatterns": [
      "(?i)(password|passwd|pwd)\\s*[=:]",
      "(?i)(secret|token|api[_-]?key)\\s*[=:]"
    ]
  }
}
```

### Logging Configuration

```json
{
  "logging": {
    "enforceCriticalLogging": true,
    "warnOnDebugInProduction": true,
    "minLogLevel": "INFO",
    "requiredLogPoints": [
      "methodEntry",
      "methodExit",
      "databaseOperations",
      "exceptions"
    ]
  }
}
```

---

## Team Profile Configurations

### Startup Profile (Fast Iteration)

**Use for**: Early-stage development, rapid prototyping, MVP

```json
{
  "profile": "startup",
  "qualityGate": {
    "criticalIssuesAllowed": 0,
    "highIssuesAllowed": 10,
    "mediumIssuesAllowed": 20,
    "minTestCoveragePercent": 50
  },
  "rules": {
    "PublicAPIJavaDoc": { "enabled": false },
    "MethodLength": { "enabled": false },
    "ClassSize": { "enabled": false }
  }
}
```

### Enterprise Profile (Strict Standards)

**Use for**: Production-grade applications, regulated industries

```json
{
  "profile": "enterprise",
  "qualityGate": {
    "criticalIssuesAllowed": 0,
    "highIssuesAllowed": 1,
    "mediumIssuesAllowed": 3,
    "minTestCoveragePercent": 85
  },
  "rules": {
    "PublicAPIJavaDoc": { "enabled": true, "severity": "Medium" },
    "MethodLength": { "threshold": 30, "severity": "Medium" },
    "ClassSize": { "maxLines": 200, "severity": "Medium" },
    "CyclomaticComplexity": { "threshold": 8, "severity": "High" },
    "MissingInputValidation": { "severity": "Critical" }
  }
}
```

### Fintech Profile (Maximum Security)

**Use for**: Financial services, security-critical applications

```json
{
  "profile": "fintech",
  "qualityGate": {
    "criticalIssuesAllowed": 0,
    "highIssuesAllowed": 0,
    "mediumIssuesAllowed": 1,
    "minTestCoveragePercent": 90
  },
  "rules": {
    "SQLInjectionRisk": { "enabled": true, "severity": "Critical" },
    "HardcodedSecrets": { "enabled": true, "severity": "Critical" },
    "WeakCrypto": { "enabled": true, "severity": "Critical" },
    "MissingAuthenticationGuard": { "enabled": true, "severity": "Critical" },
    "CyclomaticComplexity": { "threshold": 6, "severity": "High" }
  },
  "security": {
    "checkHardcodedSecrets": true,
    "checkSQLInjection": true,
    "checkXSS": true,
    "checkAuthenticationBypass": true,
    "checkWeakCrypto": true,
    "checkInsecureDeserialization": true
  }
}
```

---

## Framework-Specific Configuration

### Spring Boot Application

```json
{
  "framework": "spring-boot",
  "rules": {
    "MissingRepositoryInterface": { "enabled": true },
    "MissingServiceAnnotation": { "enabled": true },
    "ControllerBusinessLogic": { "enabled": true, "severity": "High" },
    "MissingAutowiredValidation": { "enabled": true }
  },
  "exclusions": {
    "patterns": [
      "**/config/**",
      "**/*Config.java"
    ]
  }
}
```

### JPA/Hibernate Application

```json
{
  "orm": "hibernate",
  "rules": {
    "NPlusOneQueryProblem": { "enabled": true, "severity": "High" },
    "MissingEagerLoading": { "enabled": true, "severity": "High" },
    "OpenSessionInViewAntiPattern": { "enabled": true, "severity": "High" },
    "LazyInitializationException": { "enabled": true, "severity": "Medium" }
  },
  "database": {
    "checkNPlus1": true,
    "checkMissingEagerLoading": true,
    "checkMissingIndexes": true
  }
}
```

---

## CI/CD Integration Configuration

### GitHub Actions Integration

```json
{
  "cicd": {
    "type": "github-actions",
    "postResults": true,
    "postAsComment": true,
    "failOnQualityGateFail": true,
    "artifactName": "java-review-report",
    "includeLogs": true
  }
}
```

### GitLab CI Integration

```json
{
  "cicd": {
    "type": "gitlab-ci",
    "postResults": true,
    "createJunitReport": true,
    "failOnQualityGateFail": true,
    "reportPath": "target/review-report.xml"
  }
}
```

---

## Example Configurations

### Minimal Configuration

```json
{
  "projectType": "java"
}
```

### Recommended Configuration

```json
{
  "projectType": "java",
  "buildTool": "maven",
  "qualityGate": {
    "criticalIssuesAllowed": 0,
    "highIssuesAllowed": 3,
    "mediumIssuesAllowed": 10,
    "minTestCoveragePercent": 70
  },
  "exclusions": {
    "files": ["**/test/**", "**/generated/**"],
    "directories": ["target/", "build/"]
  }
}
```

### Strict Configuration

```json
{
  "projectType": "java",
  "buildTool": "maven",
  "qualityGate": {
    "criticalIssuesAllowed": 0,
    "highIssuesAllowed": 1,
    "mediumIssuesAllowed": 5,
    "minTestCoveragePercent": 85
  },
  "complexity": {
    "cyclomaticComplexity": { "threshold": 8 },
    "methodLength": { "maxLines": 30 },
    "classSize": { "maxLines": 200 }
  },
  "rules": {
    "PublicAPIJavaDoc": { "enabled": true },
    "StringConcatenationInLoop": { "severity": "Critical" },
    "SQLInjectionRisk": { "severity": "Critical" }
  }
}
```

---

## Troubleshooting Configuration

### Issue: Rules Not Triggering

**Solution**: Check that rule is enabled in config:
```json
{
  "rules": {
    "RuleName": {
      "enabled": true  // Ensure enabled: true
    }
  }
}
```

### Issue: Too Many False Positives

**Solution**: Adjust severity or add exclusions:
```json
{
  "rules": {
    "ProblemRule": {
      "enabled": true,
      "severity": "Info"  // Lower severity
    }
  },
  "exclusions": {
    "patterns": ["**/generated/**"]  // Exclude generated code
  }
}
```

### Issue: Quality Gate Too Strict

**Solution**: Increase allowed thresholds gradually:
```json
{
  "qualityGate": {
    "highIssuesAllowed": 5,      // Increased from 3
    "mediumIssuesAllowed": 15    // Increased from 10
  }
}
```

---

## Validation

Validate configuration before use:

```bash
# Validate JSON syntax
java -jar json-validator.jar .codereview.config.json

# Check configuration completeness
skill-invoke java-review --validate-config
```

---

## Next Steps

1. Save your configuration to `.codereview.config.json`
2. Test it: `skill-invoke java-review --config validate`
3. Use in CI/CD pipeline or local development
4. Adjust thresholds based on team feedback

See [RULESET.md](RULESET.md) for detailed rule information and customization options.
