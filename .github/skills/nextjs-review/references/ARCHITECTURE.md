# Next.js Code Review Skill Architecture

Internal architecture and design of the Next.js Code Review skill.

---

## System Overview

```
┌─────────────────────────────────────────────────────────────┐
│               User Input (PR/Branch)                        │
└────────────────────┬────────────────────────────────────────┘
                     ↓
┌─────────────────────────────────────────────────────────────┐
│           Input Parser & Validator                          │
│  - Parse PR URLs, numbers, branch names                     │
│  - Validate Next.js project (package.json, app/pages/)      │
│  - Authenticate with GitHub/GitLab/Bitbucket               │
└────────────────────┬────────────────────────────────────────┘
                     ↓
┌─────────────────────────────────────────────────────────────┐
│           Git Repository Setup                              │
│  - Clone/fetch repository                                   │
│  - Checkout source/destination branches                     │
│  - Extract commit metadata                                  │
└────────────────────┬────────────────────────────────────────┘
                     ↓
┌─────────────────────────────────────────────────────────────┐
│           Diff & File Analysis                              │
│  - Generate git diff between branches                       │
│  - Categorize changed files (tsx, js, config, tests)        │
│  - Extract changed functions/components                     │
└────────────────────┬────────────────────────────────────────┘
                     ↓
┌─────────────────────────────────────────────────────────────┐
│           Multi-Analyzer Pipeline                           │
│  ┌──────────────────────────────────────────────────────┐   │
│  │ 1. Code Style & Conventions Analyzer               │   │
│  │    - Naming conventions (camelCase, CONSTANT_CASE) │   │
│  │    - Import organization (Next.js best practices)  │   │
│  │    - File structure (app/pages/ organization)      │   │
│  │    - JSDoc/TSDoc coverage                          │   │
│  └──────────────────────────────────────────────────────┘   │
│  ┌──────────────────────────────────────────────────────┐   │
│  │ 2. Architecture & Patterns Analyzer                 │   │
│  │    - Component composition (RSC vs Client)          │   │
│  │    - Server/Client component boundaries            │   │
│  │    - Hook usage patterns                           │   │
│  │    - Dependency injection & context usage          │   │
│  │    - State management patterns                      │   │
│  │    - Route organization                            │   │
│  └──────────────────────────────────────────────────────┘   │
│  ┌──────────────────────────────────────────────────────┐   │
│  │ 3. Complexity & Maintainability Analyzer            │   │
│  │    - Cyclomatic complexity of functions            │   │
│  │    - Component props interface complexity          │   │
│  │    - Nesting depth analysis                        │   │
│  │    - Function length (lines of code)               │   │
│  │    - Comment-to-code ratio                         │   │
│  └──────────────────────────────────────────────────────┘   │
│  ┌──────────────────────────────────────────────────────┐   │
│  │ 4. Performance Analyzer                             │   │
│  │    - Unnecessary re-renders (deps arrays)          │   │
│  │    - Image optimization (Next.js Image component) │   │
│  │    - Font loading strategy                         │   │
│  │    - Bundle size impact                            │   │
│  │    - Lazy loading & code splitting                │   │
│  │    - API route performance patterns                │   │
│  └──────────────────────────────────────────────────────┘   │
│  ┌──────────────────────────────────────────────────────┐   │
│  │ 5. Security Analyzer                                │   │
│  │    - OWASP Top 10 issues                            │   │
│  │    - SQL injection vulnerabilities                 │   │
│  │    - XSS prevention patterns                        │   │
│  │    - Authentication/authorization                  │   │
│  │    - Secrets/tokens in code                        │   │
│  │    - Next.js security headers (CSP, CORS)         │   │
│  │    - Environment variable handling                 │   │
│  └──────────────────────────────────────────────────────┘   │
│  ┌──────────────────────────────────────────────────────┐   │
│  │ 6. Database & Query Analyzer                        │   │
│  │    - SQL query patterns & optimization             │   │
│  │    - N+1 query detection                           │   │
│  │    - Database transaction handling                 │   │
│  │    - Prepared statements & parameterization       │   │
│  │    - ORM usage patterns (Prisma, Drizzle)         │   │
│  │    - Migration scripts validation                  │   │
│  └──────────────────────────────────────────────────────┘   │
└────────────────────┬────────────────────────────────────────┘
                     ↓
┌─────────────────────────────────────────────────────────────┐
│           Severity Ranking & Prioritization                │
│  - Critical: Security, data loss, production impact        │
│  - High: Performance, reliability, major architecture      │
│  - Medium: Code quality, maintainability, style            │
│  - Low: Minor style, documentation improvements           │
│  - Informational: Suggestions, best practices             │
└────────────────────┬────────────────────────────────────────┘
                     ↓
┌─────────────────────────────────────────────────────────────┐
│           Quality Gate Evaluation                           │
│  - Pass/Fail decision based on finding severity            │
│  - Coverage metrics                                         │
│  - Test quality assessment                                  │
│  - Configuration compliance                                │
└────────────────────┬────────────────────────────────────────┘
                     ↓
┌─────────────────────────────────────────────────────────────┐
│           Report Generation                                 │
│  - Executive summary                                        │
│  - Detailed findings with code snippets                    │
│  - Recommendations & fix suggestions                       │
│  - Metrics & statistics                                    │
│  - Quality gate result                                     │
└────────────────────┬────────────────────────────────────────┘
                     ↓
┌─────────────────────────────────────────────────────────────┐
│               Review Report Output                          │
│  (HTML, Markdown, JSON)                                     │
└─────────────────────────────────────────────────────────────┘
```

---

## Core Components

### 1. Input Parser (`inputParser.ts`)

Responsible for normalizing and validating user input.

```typescript
class InputParser {
  // Parse PR links and extract metadata
  parsePRLink(url: string): ParsedPR {
    // Regex patterns for GitHub, GitLab, Bitbucket URLs
    // Returns: { platform, owner, repo, prNumber }
  }

  // Detect if input is PR number, branch name, or URL
  detectInputType(input: string): "pr_url" | "pr_number" | "branch_name" {
    // Pattern matching and validation
  }

  // Validate Next.js project structure
  validateNextJsProject(repoPath: string): ValidationResult {
    // Check: package.json with "next" dep
    // Check: app/ or pages/ directory
    // Check: tsconfig.json or jsconfig.json
  }
}
```

### 2. Git Repository Handler (`gitRepository.ts`)

Manages git operations and branch management.

```typescript
class GitRepository {
  // Clone repository from given URL
  async clone(repoUrl: string): Promise<string> {
    // Uses git clone, returns cloned directory path
  }

  // Fetch PR metadata from Git platform APIs
  async fetchPRMetadata(
    platform: string,
    owner: string,
    repo: string,
    prNumber: number
  ): Promise<PRMetadata> {
    // Calls GitHub/GitLab/Bitbucket API
    // Returns: source branch, dest branch, commit info
  }

  // Get diff between two branches
  async getDiff(sourceBranch: string, destBranch: string): Promise<string> {
    // git diff sourceBranch...destBranch --unified=3
  }

  // Extract changed files
  async getChangedFiles(sourceBranch: string, destBranch: string): Promise<File[]> {
    // git diff --name-status
    // Categorizes files: added, modified, deleted
  }

  // Get commits in PR
  async getPRCommits(sourceBranch: string, destBranch: string): Promise<Commit[]> {
    // git log destBranch..sourceBranch
  }
}
```

### 3. File Analyzer (`fileAnalyzer.ts`)

Extracts AST and analyzes individual files.

```typescript
class FileAnalyzer {
  // Parse TypeScript/JavaScript files into AST
  parseFile(filePath: string): AST {
    // Uses @typescript-eslint parser or similar
    // Returns abstract syntax tree
  }

  // Extract exported functions/components
  extractExports(ast: AST): ExportedSymbol[] {
    // Walks AST to find: export default, export const, etc.
  }

  // Analyze component props interface
  analyzeComponentProps(componentNode: AST): PropAnalysis {
    // Extracts prop interface complexity
    // Flags overly complex prop objects
  }

  // Detect component type (RSC vs Client)
  detectComponentType(filePath: string, content: string): "rsc" | "client" {
    // Checks for "use client" directive
    // Checks for hook usage
  }

  // Extract JSDoc/TSDoc comments
  extractDocumentation(ast: AST): DocumentationInfo {
    // Finds JSDoc blocks
    // Extracts description, param types, return type
  }
}
```

### 4. Multi-Analyzer Pipeline (`analyzers/`)

Six specialized analysis modules working in parallel:

#### 4a. Style & Conventions Analyzer
```typescript
class StyleAnalyzer {
  // Check naming conventions
  checkNamingConventions(ast: AST): Finding[] {
    // camelCase for variables/functions
    // PascalCase for components/classes
    // CONSTANT_CASE for constants
  }

  // Check import organization
  checkImportOrganization(ast: AST): Finding[] {
    // Order: React, Next.js, external, internal
    // Group into logical sections
  }

  // Check JSDoc coverage
  checkDocumentation(ast: AST): Finding[] {
    // Flag functions/components without JSDoc
    // Check for missing @param, @returns
  }
}
```

#### 4b. Architecture Analyzer
```typescript
class ArchitectureAnalyzer {
  // Check Server vs Client component patterns
  analyzeComponentBoundaries(files: File[]): Finding[] {
    // Warn if Client Component has only Server children
    // Flag Server Context in Client components
  }

  // Validate route organization
  checkRouteOrganization(appDir: string): Finding[] {
    // Check routes follow Next.js conventions
    // Flag unusual directory structure
  }

  // Analyze hook usage patterns
  checkHookPatterns(ast: AST): Finding[] {
    // Flag hooks in conditional branches (bad)
    // Flag missing dependency arrays
  }

  // Check dependency injection patterns
  checkDependencyInjection(ast: AST): Finding[] {
    // Analyze context providers
    // Check prop-drilling issues
  }
}
```

#### 4c. Complexity Analyzer
```typescript
class ComplexityAnalyzer {
  // Calculate cyclomatic complexity
  calculateCyclomaticComplexity(ast: AST): ComplexityData[] {
    // Count decision points (if/switch/loops)
    // Flag functions with complexity > 10
  }

  // Measure function length
  checkFunctionLength(ast: AST): Finding[] {
    // Flag functions > 50 lines
    // Suggestion to refactor
  }

  // Analyze nesting depth
  checkNestingDepth(ast: AST): Finding[] {
    // Flag deeply nested code (>4 levels)
  }

  // Component props complexity
  checkPropsComplexity(componentNode: AST): Finding[] {
    // Count prop properties
    // Flag > 10 props as code smell
  }
}
```

#### 4d. Performance Analyzer
```typescript
class PerformanceAnalyzer {
  // Check dependency arrays
  checkDependencies(ast: AST): Finding[] {
    // Detect missing or extra dependencies in useEffect/useMemo/useCallback
    // Flag stale closures
  }

  // Image optimization checks
  checkImageOptimization(ast: AST): Finding[] {
    // Flag <img> instead of Next.js Image component
    // Check for missing width/height props
    // Verify responsive images with srcSet
  }

  // Font loading strategy
  checkFontStrategy(appDir: string): Finding[] {
    // Check next/font usage
    // Flag inefficient font loading
  }

  // Check lazy loading
  checkLazyLoading(ast: AST): Finding[] {
    // Recommend dynamic() for code splitting
    // Suggest React.lazy() patterns
  }

  // Analyze API route performance
  analyzeAPIPerformance(filePath: string, content: string): Finding[] {
    // Check for missing caching headers
    // Verify request validation
  }
}
```

#### 4e. Security Analyzer
```typescript
class SecurityAnalyzer {
  // OWASP Top 10 checks
  checkOWASPVulnerabilities(ast: AST): Finding[] {
    // Injection attacks, broken auth, XSS, CSRF, etc.
  }

  // SQL injection detection
  checkSQLInjection(ast: AST, dbCode: string[]): Finding[] {
    // Flag concatenated SQL queries
    // Verify parameterized statements
  }

  // XSS prevention
  checkXSSPrevention(ast: AST): Finding[] {
    // Flag dangerous innerHTML usage
    // Check for dangerouslySetInnerHTML
  }

  // Environment variable handling
  checkEnvVars(files: File[]): Finding[] {
    // Flag hardcoded secrets
    // Verify NEXT_PUBLIC_ prefix for client vars
    // Check .env.local usage
  }

  // Security headers
  checkSecurityHeaders(configFile: string): Finding[] {
    // next.config.js headers configuration
    // CSP, X-Frame-Options, X-Content-Type-Options
  }

  // Authentication check
  checkAuthPatterns(apiRoutes: File[]): Finding[] {
    // Verify auth middleware
    // Check session validation
  }
}
```

#### 4f. Database & Query Analyzer
```typescript
class DatabaseAnalyzer {
  // Analyze SQL queries
  analyzeSQLQueries(code: string[]): Finding[] {
    // Parse SQL syntax
    // Identify potential optimizations
    // Check for parameterization
  }

  // N+1 query detection
  checkN1Queries(ast: AST, dbCode: string[]): Finding[] {
    // Pattern: loop calling database
    // Suggest batch loading or JOINs
  }

  // ORM usage patterns (Prisma, Drizzle)
  checkORM(filePath: string, content: string): Finding[] {
    // Verify select() used for column projection
    // Check for relation loading strategies
    // Flag select n+1 in Prisma
  }

  // Transaction analysis
  checkTransactions(dbCode: string[]): Finding[] {
    // Flag missing transactions where needed
    // Check for proper error handling
  }

  // Migration validation
  checkMigrations(migrationDir: string): Finding[] {
    // Validate SQL syntax
    // Flag risky operations (DROP, ALTER)
  }
}
```

### 5. Severity Ranker (`severityRanker.ts`)

Prioritizes findings by impact and severity.

```typescript
class SeverityRanker {
  // Rank findings by severity
  rankFindings(findings: Finding[]): RankedFinding[] {
    // CRITICAL: Security, data loss, unavailability
    // HIGH: Major perf issues, reliability problems
    // MEDIUM: Code quality, maintainability
    // LOW: Style, minor improvements
    // INFO: Suggestions
  }

  // Calculate quality gate result
  evaluateQualityGate(rankedFindings: RankedFinding[]): QualityGateResult {
    // PASS: No Critical, ≤3 High
    // WARN: 1-2 Critical, ≤5 High
    // FAIL: 3+ Critical or 6+ High
  }

  // Generate metrics
  calculateMetrics(findings: Finding[], files: File[]): Metrics {
    // Issues per file
    // Coverage statistics
    // Code quality score (0-100)
  }
}
```

### 6. Report Generator (`reportGenerator.ts`)

Creates comprehensive review reports.

```typescript
class ReportGenerator {
  // Generate HTML report
  generateHTMLReport(
    findings: RankedFinding[],
    metrics: Metrics,
    qualityGate: QualityGateResult
  ): string {
    // Styled HTML with:
    // - Executive summary
    // - Findings grouped by severity
    // - Code snippets
    // - Metrics visualizations
  }

  // Generate Markdown report
  generateMarkdownReport(...): string {
    // For GitHub comments, emails
    // Easy to read, searchable
  }

  // Generate JSON report
  generateJSONReport(...): object {
    // For CI/CD integration
    // Machine-readable format
  }

  // Generate summary snippet
  generateSummary(...): string {
    // Concise 2-3 line summary
    // For chat/inline display
  }
}
```

---

## Data Flow Examples

### Example 1: Full PR Review

```
User inputs: https://github.com/acme/dashboard/pull/427

1. InputParser
   - Parses URL → platform=GitHub, pr=427, repo=acme/dashboard
   
2. GitRepository
   - Fetches PR metadata → source=feature/auth, dest=main
   - Clones repo
   - Gets changeset: 15 files modified (8 .tsx, 3 .ts, 4 config)
   
3. FileAnalyzer (parallel on 15 files)
   - Parses each file into AST
   - Extracts components, functions, imports
   
4. Multi-Analyzer Pipeline (parallel on all findings)
   - StyleAnalyzer: 3 findings (naming, imports)
   - ArchitectureAnalyzer: 2 findings (RSC pattern)
   - ComplexityAnalyzer: 1 finding (complex function)
   - PerformanceAnalyzer: 4 findings (missing deps, bad Image)
   - SecurityAnalyzer: 2 findings (hardcoded secret)
   - DatabaseAnalyzer: 1 finding (N+1 query)
   
   Total: 13 findings
   
5. SeverityRanker
   - 1 CRITICAL (hardcoded secret)
   - 2 HIGH (N+1 query, complex function)
   - 4 MEDIUM (architecture, performance)
   - 6 LOW (style, suggestions)
   
   QualityGate: WARN (1 critical)
   
6. ReportGenerator
   - Creates HTML/MD report with all findings
   - Includes code snippets for each
   - Prioritized by severity
```

### Example 2: Branch Comparison

```
User inputs: feature/api-redesign
(from within project directory)

1. InputParser
   - Detects branch name
   - Validates next.js project in cwd
   
2. GitRepository
   - Validates branch exists
   - Gets default branch → main
   - Gets changeset for feature/api-redesign → main
   
3-6. Same as Example 1
```

---

## Parallelization Strategy

The architecture leverages parallel execution for performance:

```
Input Parsing              (sequential)
     ↓
Git Operations            (sequential - needed for data)
     ↓
┌────────────────────────────────────────────────────────┐
│ File Parsing (parallel)  - Parse all files in parallel │
└────────────────────────────────────────────────────────┘
     ↓
┌──────────┬─────────────┬──────────────┬──────────────┐
│StyleAnal.│ArchAnal.   │ ComplexAnal. │ PerfAnal.    │ (parallel)
├──────────┼─────────────┼──────────────┼──────────────┤
│SecAnal.  │ DatabaseAnal.                             │
└──────────┴─────────────────────────────────────────────┘
     ↓
Severity Ranking          (sequential)
     ↓
Report Generation         (sequential)
```

**Performance**: For a PR with 15-20 files:
- Sequential: ~45-60 seconds
- With parallelization: ~15-20 seconds (3x faster)

---

## Extension Points

The architecture is designed for extensibility:

### Adding New Analyzer
```typescript
// 1. Create new analyzer class extending BaseAnalyzer
class AccessibilityAnalyzer extends BaseAnalyzer {
  async analyze(files: File[]): Promise<Finding[]> {
    // Custom analysis logic
  }
}

// 2. Register in pipeline
pipeline.registerAnalyzer(new AccessibilityAnalyzer())

// 3. Add severity definitions
SeverityRanker.registerRules({
  "a11y/missing-alt-text": "HIGH",
  "a11y/poor-contrast": "MEDIUM"
})
```

### Custom Report Format
```typescript
// Create custom report generator
class SlackReportGenerator extends ReportGenerator {
  generateReport(...): string {
    // Format findings as Slack blocks
    // Return JSON for Slack API
  }
}
```

### Custom Severity Rules
```typescript
// Override severity for specific issues
severityRanker.setCustomSeverity({
  "security/hardcoded-secret": "CRITICAL",
  "perf/image-optimization": "HIGH"
})
```

---

## Performance Characteristics

| Operation | Time | Notes |
|-----------|------|-------|
| Input parsing | 100ms | Fast URL/string matching |
| Git clone (small repo) | 5-15s | Depends on repo size |
| Git clone (large repo) | 30-60s | 500MB+ repos |
| File parsing (50 files) | 2-5s | Parallel AST parsing |
| Analysis (6 analyzers) | 10-20s | Parallel analysis |
| Report generation | 1-2s | HTML/MD/JSON serialization |
| **Total (50-file PR)** | **20-30s** | End-to-end |

---

## Error Recovery

The system handles common failures gracefully:

```
If git clone fails:
  → Try git fetch if repo already exists
  → Fall back to shallow clone (--depth=1)
  → Ask user for SSH key if HTTPS fails

If parsing fails on file:
  → Log error, skip file, continue
  → Report parsing error in output

If API rate limit hit:
  → Cache results, retry after delay
  → Use lower-res results if available

If analysis times out (>5 min):
  → Return partial results
  → Mark incomplete files in report
```

---

## Security Considerations

### API Token Handling
- Tokens stored in environment variables only
- Never logged or included in output
- Revoked after analysis if temporary token

### Repository Access
- Only clone/fetch, never modify
- Use read-only operations
- Clean up temporary files after analysis

### Code Analysis
- No execution of user code
- Pure static analysis via AST
- No network calls from analyzed code

### Output Sanitization
- Strip credentials from output
- Sanitize code snippets in reports
- No PII in report files

---

## Testing Architecture

```
Unit Tests:
  ✓ InputParser.parseURL()
  ✓ StyleAnalyzer.checkNaming()
  ✓ SeverityRanker.rankFindings()

Integration Tests:
  ✓ Full PR review flow (mock API)
  ✓ Branch comparison flow
  ✓ Report generation

E2E Tests:
  ✓ Actual GitHub PR review
  ✓ GitLab merge request review
  ✓ Real Next.js project analysis
```
