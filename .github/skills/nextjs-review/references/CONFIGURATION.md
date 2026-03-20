# Next.js Code Review - Configuration Guide

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
      "nextjs-conventions",
      "code-architecture",
      "complexity-analysis",
      "performance-issues",
      "api-routes",
      "database-fetching",
      "security-issues",
      "react-best-practices",
      "testing-documentation"
    ],
    "severity-overrides": {
      "missing-image-optimization": "high",
      "unused-import": "low",
      "missing-error-boundary": "high",
      "missing-suspense-boundary": "high"
    },
    "ignored-patterns": [
      "**/node_modules/**",
      "**/.next/**",
      "**/dist/**",
      "**/*.spec.tsx",
      "**/*.test.tsx",
      "**/__tests__/**"
    ]
  },
  "nextjs": {
    "appRouter": true,
    "requireServerComponents": true,
    "enforceImageOptimization": true,
    "requireMetadata": true,
    "requireErrorBoundaries": true,
    "requireLoadingStates": true,
    "enforceSuspenseBoundaries": true
  },
  "complexity": {
    "maxCyclomaticComplexity": 10,
    "maxCognitiveComplexity": 15,
    "maxComponentLength": 200,
    "maxJsxNestingDepth": 6,
    "maxPropsDrilling": 3,
    "maxHookDependencies": 10
  },
  "testing": {
    "minCoveragePercent": 70,
    "requiredTestFiles": [
      "**/*.utility.ts",
      "**/*.hook.ts",
      "**/lib/**"
    ],
    "ignoredFiles": [
      "**/page.tsx",
      "**/layout.tsx",
      "**/error.tsx"
    ]
  },
  "security": {
    "blockCriticalSecurityIssues": true,
    "requiredSecurityRules": [
      "hardcoded-credentials",
      "xss-vulnerability",
      "missing-authentication",
      "missing-input-validation",
      "sql-injection-risk"
    ],
    "allowedAuthLibs": ["next-auth", "auth0", "clerk"],
    "forbiddenLibraries": ["md5", "crypto-js"]
  },
  "performance": {
    "flagImagesWithoutOptimization": true,
    "requireDynamicImports": true,
    "flagLargeBundle": true,
    "maxBundleSizeMb": 500,
    "warnAboveChunkSize": 250
  },
  "api": {
    "requireMethodValidation": true,
    "requireInputValidation": true,
    "requireErrorHandling": true,
    "requireRateLimiting": true,
    "requireLogging": true,
    "requireCors": true
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
  "maxAverageCyclomaticComplexity": 10,  // Avg across all components
  "maxAverageCognitiveComplexity": 15,
  "minTestCoveragePercent": 70,       // Min code coverage required
  "maxDuplicationPercent": 3          // Max code duplication allowed
}
```

**Guidelines**:
- **Strict (enterprise)**: Critical=0, High=0, Medium=5, Coverage=80%
- **Moderate (startups)**: Critical=0, High=3, Medium=10, Coverage=70%
- **Relaxed (prototypes)**: Critical=0, High=5, Medium=20, Coverage=50%

### Next.js Configuration

```json
"nextjs": {
  "appRouter": true,                  // Enforce App Router (not Pages Router)
  "requireServerComponents": true,    // Flag client-component-only patterns
  "enforceImageOptimization": true,   // Require Image component usage
  "requireMetadata": true,            // Each page should export metadata
  "requireErrorBoundaries": true,     // Require error.tsx files
  "requireLoadingStates": true,       // Require loading.tsx for slow routes
  "enforceSuspenseBoundaries": true   // Require Suspense for async operations
}
```

### Complexity Thresholds

```json
"complexity": {
  "maxCyclomaticComplexity": 10,      // Max branches in single function
  "maxCognitiveComplexity": 15,       // Max mental effort score
  "maxComponentLength": 200,          // Max lines in component
  "maxJsxNestingDepth": 6,            // Max JSX nesting levels
  "maxPropsDrilling": 3,              // Max prop drilling levels
  "maxHookDependencies": 10           // Max dependencies in useEffect
}
```

**Rules of thumb**:
- Cyclomatic complexity > 10 = hard to test
- Component > 200 lines = likely doing too much
- Props drilling > 3 levels = use Context or state management
- Hook dependencies > 8 = consider breaking into smaller effects

### Security Configuration

```json
"security": {
  "blockCriticalSecurityIssues": true,       // Fail PR if critical security issue
  "requiredSecurityRules": [
    "hardcoded-credentials",
    "xss-vulnerability",
    "missing-authentication"
  ],
  "allowedAuthLibs": ["next-auth", "clerk"],  // Whitelist auth libs
  "forbiddenLibraries": ["md5"]               // Blacklist unsafe libs
}
```

### Testing Configuration

```json
"testing": {
  "minCoveragePercent": 70,
  "requiredTestFiles": [
    "**/*.utility.ts",      // All utilities must be tested
    "**/*.hook.ts"         // All custom hooks must be tested
  ],
  "ignoredFiles": [
    "**/page.tsx",         // Don't require tests for pages
    "**/layout.tsx"        // Don't require tests for layouts
  ]
}
```

### Performance Configuration

```json
"performance": {
  "flagImagesWithoutOptimization": true,  // Flag unoptimized img tags
  "requireDynamicImports": true,          // Require dynamic() for heavy components
  "flagLargeBundle": true,                // Flag large bundle impacts
  "maxBundleSizeMb": 500,                 // Total bundle size limit
  "warnAboveChunkSize": 250               // Warn if chunk > 250MB
}
```

### API Routes Configuration

```json
"api": {
  "requireMethodValidation": true,    // POST/GET explicit handling
  "requireInputValidation": true,     // Validate request body
  "requireErrorHandling": true,       // try-catch in async routes
  "requireRateLimiting": true,        // Rate limiting on public endpoints
  "requireLogging": true,             // Log important operations
  "requireCors": true                 // Explicit CORS handling
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
- `suggestFixes: false` => Do not include fix suggestions.
- Line numbers are always required for every issue and cannot be disabled.

---

## Team-Specific Profiles

### Profile: Startup (Fast iteration)
```json
{
  "qualityGate": {
    "criticalIssuesAllowed": 0,
    "highIssuesAllowed": 5,
    "mediumIssuesAllowed": 20,
    "minTestCoveragePercent": 50
  },
  "nextjs": {
    "requireErrorBoundaries": false,
    "requireLoadingStates": false
  },
  "security": {
    "blockCriticalSecurityIssues": true
  }
}
```

### Profile: Enterprise (Strict standards)
```json
{
  "qualityGate": {
    "criticalIssuesAllowed": 0,
    "highIssuesAllowed": 0,
    "mediumIssuesAllowed": 5,
    "minTestCoveragePercent": 80
  },
  "nextjs": {
    "appRouter": true,
    "requireServerComponents": true,
    "requireErrorBoundaries": true,
    "requireLoadingStates": true,
    "enforceSuspenseBoundaries": true
  },
  "complexity": {
    "maxCyclomaticComplexity": 8,
    "maxComponentLength": 150
  },
  "api": {
    "requireMethodValidation": true,
    "requireInputValidation": true,
    "requireErrorHandling": true,
    "requireRateLimiting": true,
    "requireLogging": true
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
  "nextjs": {
    "appRouter": true,
    "requireServerComponents": true,
    "enforceImageOptimization": true,
    "requireErrorBoundaries": true,
    "requireLoadingStates": true,
    "enforceSuspenseBoundaries": true
  },
  "security": {
    "blockCriticalSecurityIssues": true,
    "requiredSecurityRules": [
      "hardcoded-credentials",
      "xss-vulnerability",
      "missing-authentication",
      "missing-authorization",
      "missing-input-validation",
      "unsafe-file-upload",
      "sql-injection-risk"
    ]
  },
  "api": {
    "requireMethodValidation": true,
    "requireInputValidation": true,
    "requireErrorHandling": true,
    "requireRateLimiting": true,
    "requireLogging": true,
    "requireCors": true
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

# Next.js
export REVIEW_REQUIRE_SERVER_COMPONENTS=true
export REVIEW_ENFORCE_IMAGE_OPT=true
export REVIEW_REQUIRE_ERROR_BOUNDARIES=true

# Complexity
export REVIEW_MAX_COMPLEXITY=10
export REVIEW_MAX_COMPONENT_LENGTH=200

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
// nextjs-review:disable-next-line no-client-in-route
'use client'

// nextjs-review:disable no-client-in-route
const ClientComponent = () => {
  // component code
}
// nextjs-review:enable

// nextjs-review:severity low missing-jsdoc
export function myFunction() {}
```

---

## Configuration Hierarchy

Configs are applied in order (later overrides earlier):

1. **Default config** (built-in defaults)
2. **`.codereview.config.template.json`** (in assets/)
3. **`.codereview.config.json`** (in project root)
4. **`.codereview.${PROFILE}.json`** (startup/enterprise/fintech)
5. **Environment variables**
6. **Per-file comments**

---

## Troubleshooting Configuration

### Config not loading
```bash
# Verify JSON syntax
node -e "JSON.parse(require('fs').readFileSync('.codereview.config.json'))"

# Check file location
ls -la .codereview.config.json
```

### Changes not taking effect
1. Ensure file is in project root (not nested)
2. Check JSON syntax is valid
3. Verify environment variable isn't overriding
4. Restart CLI/editor if cached

### Too many false positives
1. Add files to `ignored-patterns`
2. Adjust severity in `severity-overrides`
3. Increase thresholds in `qualityGate`
4. Use per-file comments to disable specific rules
