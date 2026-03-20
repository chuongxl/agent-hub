# Next.js Code Review - Configuration Profiles

Pre-built configuration profiles optimized for different organizational contexts and compliance requirements.

---

## Quick Start

Choose a profile that matches your organization:

1. **[Startup](#startup)** - Fast-moving teams prioritizing velocity
2. **[Enterprise](#enterprise)** - Balanced quality and velocity for growth-stage companies
3. **[Fintech](#fintech)** - Maximum security and compliance for financial services

## Using Profiles

### Option 1: Use Default Profile
Store `.codereview.config.json` in your project root with the profile content:

```bash
cp assets/.codereview.startup.json .codereview.config.json
```

### Option 2: Reference Profile from CLI
```bash
nextjs-review --config=assets/.codereview.startup.json
@nextjs-review https://... --profile=startup
```

### Option 3: Extend Profile
Extend a profile with custom overrides:

```json
{
  "extends": "./assets/.codereview.enterprise.json",
  "qualityGate": {
    "criticalIssuesAllowed": 1
  },
  "rules": {
    "severity-overrides": {
      "missing-jsdoc": "low"
    }
  }
}
```

---

## Profile Comparison

| Feature | Startup | Enterprise | Fintech |
|---------|---------|-----------|---------|
| **Critical Issues Allowed** | 2 | 0 | 0 |
| **High Issues Allowed** | 8 | 3 | 1 |
| **Min Test Coverage** | 50% | 70% | 85% |
| **Max Complexity** | 12 | 10 | 8 |
| **Security Checks** | Essential | Comprehensive | Exhaustive |
| **Compliance** | None | Basic | SOC-2, PCI-DSS, GDPR |
| **Audit Logging** | No | No | Yes |
| **Review Time** | 15s | 20s | 30s |

---

## Startup Profile

**For**: Seed/Series A startups, <20 people, product-market fit phase

**Philosophy**: Move fast, focus on critical issues, learn as you scale

### Key Characteristics:
- ✅ High tolerance for code quality quirks
- ✅ Prioritizes business logic over perfection
- ✅ Security/performance/databases are checked
- ⚠️ Skips documentation and style enforcement
- ⏱️ Fast reviews (15 seconds)

### Quality Gates:
```
CRITICAL issues: ≤ 2 allowed
HIGH issues: ≤ 8 allowed
MEDIUM issues: ≤ 20 allowed
Test coverage: ≥ 50%
Cyclomatic complexity: ≤ 12
```

### When to Use:
- You're shipping frequently (multiple times/day)
- Team is small and communicative
- Ship first, refactor later mentality
- Need to validate product-market fit

### When NOT to Use:
- You're handling sensitive customer data
- Team >10 developers (communication breakdown)
- Users depend on reliability (SaaS, B2B)
- You've raised funding (expect standards)

### Tips:
```bash
# Keep it light, check what matters
nextjs-review --profile=startup feature/my-change

# Focus on blockers only
nextjs-review --profile=startup --severity=critical,high
```

---

## Enterprise Profile

**For**: Growth-stage companies, 50-500 people, established products

**Philosophy**: Sustainable growth with quality standards that scale with team

### Key Characteristics:
- ✅ Balanced approach to quality and velocity
- ✅ Strong architecture and security baseline
- ✅ Comprehensive but not obsessive documentation
- ✅ Performance and accessibility matter
- ⏱️ Moderate reviews (20 seconds)

### Quality Gates:
```
CRITICAL issues: 0 allowed
HIGH issues: ≤ 3 allowed
MEDIUM issues: ≤ 10 allowed
Test coverage: ≥ 70%
Cyclomatic complexity: ≤ 10
```

### When to Use:
- Team is growing beyond startup stage
- Serving thousands of users
- Need to maintain code quality long-term
- Balancing innovation with stability
- Planning for future scale

### When NOT to Use:
- Extremely simple CRUD apps
- Financial/healthcare data handling
- Team is still <20 (use Startup)
- No architecture yet (too early)

### Tips:
```bash
# Standard review with balanced checks
nextjs-review --profile=enterprise

# Focus on quality issues
nextjs-review --profile=enterprise --focus=architecture,performance

# Exclude style issues for faster reviews
nextjs-review --profile=enterprise --skip=code-style
```

---

## Fintech Profile

**For**: Financial services, healthcare, government, highly regulated industries

**Philosophy**: Maximum security, compliance, and auditability with regulatory requirements

### Key Characteristics:
- 🔒 Exhaustive security and compliance checks
- 🔒 Mandatory audit logging and tracking
- 🔒 Regulatory compliance (PCI-DSS, SOC-2, GDPR)
- 🔒 Zero tolerance for security issues
- 🔒 Comprehensive documentation and approval chains
- ⏱️ Thorough reviews (30+ seconds)

### Quality Gates:
```
CRITICAL issues: 0 allowed (hard block)
HIGH issues: ≤ 1 allowed
MEDIUM issues: ≤ 5 allowed
Test coverage: ≥ 85%
Cyclomatic complexity: ≤ 8
Manual approval: REQUIRED
Security approval: REQUIRED
```

### Compliance Checked:
- ✅ PCI-DSS (payment card data)
- ✅ SOC-2 (security, availability, integrity)
- ✅ GDPR (personal data protection)
- ✅ HIPAA (optional, health data)

### When MUST Use:
- Handling credit card/payment data (PCI-DSS)
- Enterprise customer data
- Personal health information (HIPAA)
- EU customer data (GDPR)
- Government contracts
- Financial transactions
- Authentication/authorization code

### Sample Checks:
```typescript
// ❌ CRITICAL - Hardcoded secret
const API_SECRET = "sk_live_abc123";

// ❌ CRITICAL - SQL injection
const query = `SELECT * FROM users WHERE id = ${userId}`;

// ❌ CRITICAL - Unencrypted PII
const userData = { email, phone, ssn };

// ❌ CRITICAL - Missing audit log
export default function TransferFunds() {
  // No logging of who did what
}

// ✅ OK - Parameterized query
const query = `SELECT * FROM users WHERE id = ?`;
db.query(query, [userId]);

// ✅ OK - Encrypted PII
const userData = encrypt({ email, phone, ssn });

// ✅ OK - Audit logging
auditLog.info('Transfer initiated', {
  userId,
  amount,
  timestamp: new Date(),
  ip: req.ip
});
```

### Tips:
```bash
# Strictest possible review
nextjs-review --profile=fintech

# Require explicit approval
nextjs-review --profile=fintech --require-approval

# Generate comprehensive report with compliance details
nextjs-review --profile=fintech --format=html --include-compliance

# Archive all reports for audit trail
nextjs-review --profile=fintech --archive-report
```

---

## Customization Guide

### Extending a Profile

Create `.codereview.config.json`:

```json
{
  "extends": "./assets/.codereview.enterprise.json",
  "description": "Enterprise profile customized for our API team",
  "qualityGate": {
    "highIssuesAllowed": 5,
    "minTestCoveragePercent": 75
  },
  "rules": {
    "severity-overrides": {
      "missing-suspense-boundary": "low",
      "large-component": "low"
    },
    "ignored-patterns": [
      "**/*.generated.ts",
      "**/api/internal/**"
    ]
  }
}
```

### Creating Custom Profile

Start with a base and customize:

```json
{
  "profile": "our-api-team",
  "description": "Custom for API backend services",
  "author": "your-team",
  "version": "1.0",
  "qualityGate": {
    "criticalIssuesAllowed": 0,
    "highIssuesAllowed": 2,
    "minTestCoveragePercent": 75
  },
  "rules": {
    "enabled": [
      "api-routes",
      "database-fetching",
      "security-issues",
      "testing-documentation"
    ],
    "disabled": [
      "accessibility-compliance",
      "code-style"
    ]
  }
}
```

### Profile Per Team

Different teams can have different profiles:

```
.github/
├── codeowners
└── code-review-profiles/
    ├── web-frontend.json      # For UI components
    ├── api-backend.json       # For API routes
    ├── mobile.json            # For mobile (if applicable)
    └── fintech-payments.json  # For payment processing
```

---

## Migration Guide

### From Startup → Enterprise

When your team grows past ~20 people:

```bash
# 1. Switch to enterprise profile
cp assets/.codereview.enterprise.json .codereview.config.json

# 2. Review failing issues
nextjs-review --profile=enterprise # See what breaks

# 3. Plan remediation
# Create tickets for CRITICAL/HIGH issues

# 4. Set a date for enforcement
# Grace period: 2 weeks to fix
```

### From Enterprise → Fintech

When handling regulated data:

```bash
# 1. Enable compliance checks
cp assets/.codereview.fintech.json .codereview.config.json

# 2. Audit existing code
# Run on main branch to find issues

# 3. Implement security practices
# - Add encryption for PII
# - Add audit logging
# - Implement rate limiting

# 4. Train team on compliance
# - PCI-DSS requirements
# - GDPR data handling
# - SOC-2 controls
```

---

## Profile Settings Reference

### qualityGate
Controls whether review passes or fails:

| Setting | Type | Meaning |
|---------|------|---------|
| `criticalIssuesAllowed` | number | Max CRITICAL issues before fail |
| `highIssuesAllowed` | number | Max HIGH issues before fail |
| `mediumIssuesAllowed` | number | Max MEDIUM issues before warning |
| `minTestCoveragePercent` | number | Minimum code coverage % |
| `maxAverageCyclomaticComplexity` | number | Max function complexity score |
| `failIfQualityGateExceeds` | string | Custom fail condition |

### reviewFocus
Which rules take priority:

```json
{
  "reviewFocus": {
    "priorityRules": ["security-issues", "database-fetching"],
    "deprioritizedRules": ["code-style"],
    "mustNeverIgnore": ["hardcoded-secret", "sql-injection"]
  }
}
```

### rules
What gets checked:

```json
{
  "rules": {
    "enabled": ["nextjs-conventions", "security-issues"],
    "disabled": ["compliance-requirements"],
    "severity-overrides": {
      "missing-jsdoc": "low"
    },
    "ignored-patterns": ["**/test/**"],
    "neverIgnorePatterns": ["**/api/**"]
  }
}
```

---

## Profiles in CI/CD

### GitHub Actions Example

```yaml
name: Code Review
on: [pull_request]

jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Startup Review
        if: github.event.pull_request.labels.contains('fast-track')
        run: nextjs-review --profile=startup

      - name: Standard Review
        if: ${{ !github.event.pull_request.labels.contains('fast-track') }}
        run: nextjs-review --profile=enterprise

      - name: Comment on PR
        run: |
          # Add review comment to PR
          gh pr comment $PR_NUMBER --body-file=review.md
```

### GitLab CI Example

```yaml
code-review:
  script:
    - |
      if [ "$CI_MERGE_REQUEST_LABELS" == *"fast-track"* ]; then
        nextjs-review --profile=startup
      else
        nextjs-review --profile=enterprise
      fi
```

---

## Checklists

### Startup Team Review

- [ ] Reviewed critical/high security issues
- [ ] Database queries are parameterized
- [ ] No hardcoded secrets
- [ ] Basic performance checks pass
- [ ] Build doesn't fail

### Enterprise Team Review

- [ ] All CRITICAL issues fixed
- [ ] HIGH issues < 3
- [ ] Test coverage >= 70%
- [ ] Architecture follows patterns
- [ ] Documentation adequate
- [ ] Accessibility checked
- [ ] Performance acceptable

### Fintech Team Review

- [ ] Zero CRITICAL issues
- [ ] HIGH issues < 1
- [ ] All compliance checks pass
- [ ] Test coverage >= 85%
- [ ] Audit logging implemented
- [ ] Security review passed
- [ ] Compliance officer approves
- [ ] Report archived

---

## FAQ

**Q: Can I use a profile without a project config file?**

A: Yes! Use `--profile=startup` on CLI or select during interactive review.

**Q: What if a profile is too strict?**

A: Extend it and customize. Most teams end up with custom profiles.

**Q: Can different projects use different profiles?**

A: Yes! Add `.codereview.config.json` to each project.

**Q: When should I update to a stricter profile?**

A: When your team grows or you take on more users/responsibility.

**Q: Can I have per-developer profiles?**

A: Not recommended. Use team standards. Consistency matters.

---

**Last Updated**: 2024
**Version**: 1.0
**See Also**: [Configuration Guide](../references/CONFIGURATION.md), [SKILL.md](../SKILL.md)
