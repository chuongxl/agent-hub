# Next.js Code Review - Detailed Ruleset

Complete specification of all review rules organized by dimension and severity level.

---

## 1. Next.js Conventions & Best Practices

### Critical Issues

| Rule | Pattern | Example | Fix |
|------|---------|---------|-----|
| `missing-app-router-structure` | Invalid app directory structure | Files not in app/ with route segments | Organize routes in app/ with proper segment structure |
| `invalid-page-component` | page.tsx in wrong location | page.tsx not directly in route segment folder | Move page.tsx to correct route segment directory |
| `layout-export-error` | Layout component not exported | export default missing from layout.tsx | Add `export default function Layout()` |
| `client-component-api-call` | Direct API call in non-server component | fetch() in client component without wrapper | Use server component or API route wrapper |

### High Issues

| Rule | Pattern | Example | Fix |
|------|---------|---------|-----|
| `missing-image-optimization` | Unoptimized img tag usage | `<img src="...">` instead of Image component | Use `import Image from 'next/image'` with Next.js Image component |
| `inefficient-server-client-separation` | Excessive server-side logic in client components | Heavy data processing in use client component | Extract logic to server components or custom hooks |
| `missing-error-boundary` | No error.tsx for error handling | Route without error.tsx for error handling | Create error.tsx in route directory |
| `hardcoded-api-url` | Hardcoded API endpoints | `fetch('http://localhost:3000/api/...')` | Use environment variables (NEXT_PUBLIC_API_URL) |
| `missing-loading-state` | No loading.tsx for streaming | Loading state not defined for slow endpoints | Create loading.tsx in route directory |
| `async-data-without-suspense` | No Suspense boundary for async operations | `<Component />` with async data fetching | Wrap in Suspense with fallback |

### Medium Issues

| Rule | Pattern | Example | Fix |
|------|---------|---------|-----|
| `inefficient-routing` | Unnecessary dynamic routes | `[id].tsx` for static content | Use static pages when possible |
| `missing-middleware-validation` | API routes without input validation | Middleware without request body validation | Add validation with zod or joi |
| `unused-server-directive` | Unnecessary use server in components | `'use server'` in already server component | Remove redundant directive |
| `inconsistent-naming` | Mixed naming conventions | Mix of camelCase and PascalCase | Use consistent naming: PascalCase for components |
| `missing-metadata` | No metadata export for SEO | Page without metadata export | Add `export const metadata = { title: ... }` |

### Low Issues

| Rule | Pattern | Example | Fix |
|------|---------|---------|-----|
| `missing-jsdoc` | Public API routes without documentation | Export function without JSDoc | Add /** */ comments for public APIs |
| `incomplete-comments` | Vague or incomplete comments | `// TODO: fix this` without context | Add detailed comment with context |

---

## 2. Code Architecture & Structure

### Critical Issues

| Rule | Pattern | Example | Fix |
|------|---------|---------|-----|
| `invalid-route-segment` | Route segment violates conventions | Invalid parameter names in dynamic routes | Use valid segment names without special chars |
| `circular-dependency` | Circular imports between modules | util.ts imports from service.ts which imports util.ts | Restructure to eliminate circular dependency |
| `missing-api-response-type` | API routes without response validation | API route returns any without type | Define and use response types/interfaces |

### High Issues

| Rule | Pattern | Example | Fix |
|------|---------|---------|-----|
| `mixed-routing` | Both app/ and pages/ routers used | Routes defined in both app/ and pages/ | Choose one routing system, migrate away from pages/ |
| `business-logic-in-component` | Complex logic directly in component | Component with 20+ lines of business logic | Extract to custom hook or utility function |
| `client-directive-misplacement` | 'use client' at wrong level | Entire app marked as client on root layout | Mark only interactive components with 'use client' |
| `missing-api-error-handling` | API routes without error handling | No try-catch in async API route | Add proper error handling with appropriate status codes |
| `data-fetching-in-layout` | Fetching in layout blocking route rendering | Heavy data fetch in root layout | Use streaming or fetch at page level |

### Medium Issues

| Rule | Pattern | Example | Fix |
|------|---------|---------|-----|
| `props-drilling` | Props passed through many levels | Component passing props 3+ levels deep | Use Context API or state management |
| `file-organization` | Poor component/util file organization | All components in single components/ folder | Organize by feature (users/, posts/, etc.) |
| `missing-type-safety` | Using any instead of proper types | `const data: any = ...` | Define specific interface/type |
| `inconsistent-fetch-patterns` | Mixed data fetching approaches | Some routes use fetch, others use API calls | Standardize on server components or API routes |

---

## 3. Complexity Analysis

### Critical Issues

| Rule | Pattern | Example | Fix |
|------|---------|---------|-----|
| `excessive-hook-dependencies` | useEffect with >10 dependencies | Dependencies array with many variables | Break into smaller effects or use custom hook |
| `deeply-nested-conditionals` | Nesting depth >5 levels | Multiple levels of if/else/ternary | Extract to separate component or helper |

### High Issues

| Rule | Pattern | Example | Fix |
|------|---------|---------|-----|
| `large-component` | Component >300 lines | Single component handling too much | Split into smaller sub-components |
| `complex-render` | JSX structure >50 lines deeply nested | Complex conditional rendering | Extract sub-components or use helper functions |
| `high-cyclomatic-complexity` | Method complexity >15 | Function with 8+ conditional branches | Refactor to reduce branches, use switch/map patterns |

### Medium Issues

| Rule | Pattern | Example | Fix |
|------|---------|---------|-----|
| `deep-jsx-nesting` | JSX depth >6 levels | Many nested div/fragment wrappers | Create intermediate components |
| `long-parameter-list` | Function >4 parameters | `function(a, b, c, d, e) {}` | Use object parameter or split function |

---

## 4. Performance Issues

### Critical Issues

| Rule | Pattern | Example | Fix |
|------|---------|---------|-----|
| `missing-image-dimensions` | Image without width/height | `<Image src="..." />` missing dimensions | Add width and height props |
| `large-bundle-impact` | Heavy library imported everywhere | `import * from 'lodash'` used globally | Use tree-shakeable imports or dynamic import |
| `synchronous-blocking-operation` | Blocking operation in async context | `fs.readFileSync()` in API route | Use async version: `fs.promises.readFile()` |

### High Issues

| Rule | Pattern | Example | Fix |
|------|---------|---------|-----|
| `missing-memo-for-expensive` | Expensive component re-renders | Complex component without React.memo | Wrap with `React.memo()` or useMemo |
| `inefficient-key-usage` | Using array index as key | `key={index}` in .map() | Use unique identifier: `key={item.id}` |
| `missing-use-callback` | Event handler recreated on every render | `onClick={() => handleClick()}` | Use `useCallback` to memoize handler |
| `missing-dynamic-import` | Heavy component always imported | `import HeavyComponent from '...'` | Use `dynamic(() => import(...))` for code splitting |
| `font-loading-strategy` | Unoptimized font loading | `<link href="..." rel="stylesheet">` | Use next/font for optimized loading |

### Medium Issues

| Rule | Pattern | Example | Fix |
|------|---------|---------|-----|
| `missing-pagination` | Loading all records | Fetching all users without pagination | Implement cursor or offset pagination |
| `inefficient-state-update` | Updating entire object for one field | `setState({...state, field: newValue})` | Update only changed fields |
| `unnecessary-re-render` | Component re-rendering unnecessarily | State update causing unrelated components to render | Check dependency arrays, consider useSelector |

---

## 5. API Routes & Backend Issues

### Critical Issues

| Rule | Pattern | Example | Fix |
|------|---------|---------|-----|
| `missing-http-method-validation` | No method checking in API route | Route handler without if (req.method === 'POST') | Add method validation or use appropriate handler |
| `unvalidated-input` | No request validation | API route using req.body directly | Validate with zod, joi, or class-validator |
| `exposed-secrets` | Secrets in API route code | `const apiKey = 'sk-...'` in route file | Use environment variables |

### High Issues

| Rule | Pattern | Example | Fix |
|------|---------|---------|-----|
| `missing-auth-check` | No authentication on protected route | API route without auth verification | Add auth middleware or check |
| `missing-cors-headers` | CORS not properly configured | No CORS headers for cross-origin requests | Add CORS headers or use proper origin checks |
| `unhandled-async-error` | Promise rejection not caught | `await fetch()` without try-catch | Add error handling |
| `missing-rate-limiting` | No rate limit on public API | Public endpoint callable unlimited times | Implement rate limiting (use middleware) |

### Medium Issues

| Rule | Pattern | Example | Fix |
|------|---------|---------|-----|
| `inconsistent-response-format` | Different response structures | Some routes return `{data}`, others `{result}` | Standardize response format |
| `missing-request-logging` | No logging of API requests | No logs for audit trail | Add logging for important API calls |
| `missing-status-codes` | Incorrect HTTP status codes | Always returning 200 even for errors | Use appropriate status codes (400, 401, 500) |

---

## 6. Database & Data Fetching

### Critical Issues

| Rule | Pattern | Example | Fix |
|------|---------|---------|-----|
| `sql-injection-risk` | Raw SQL with user input | `` `SELECT * FROM users WHERE id = ${id}` `` | Use parameterized queries or ORM |
| `missing-connection-pooling` | Creating new DB connection per request | New connection in each API route | Use connection pool or client caching |

### High Issues

| Rule | Pattern | Example | Fix |
|------|---------|---------|-----|
| `n-plus-one-queries` | Loop with database query | `.map(user => db.query(...user.id))` | Use JOIN or batch fetch |
| `missing-data-validation` | No validation before DB insert | Inserting user input without sanitization | Validate/sanitize before database operation |
| `race-condition` | Concurrent operations on same resource | Two requests modifying same record | Use optimistic locking or transactions |
| `missing-index` | Frequent query on unindexed field | WHERE clause on non-indexed column | Add database index on frequently queried columns |

### Medium Issues

| Rule | Pattern | Example | Fix |
|------|---------|---------|-----|
| `inefficient-fetch` | Fetching unused columns | `SELECT *` when only 2 columns needed | Specify only needed columns |
| `missing-query-caching` | Repeated identical queries | Multiple calls with same parameters | Cache results or use SWR/TanStack Query |
| `improper-cascade` | Missing or wrong CASCADE settings | DELETE without proper FK cascade logic | Configure CASCADE or validate FK constraints |

---

## 7. Security Issues

### Critical Issues

| Rule | Pattern | Example | Fix |
|------|---------|---------|-----|
| `hardcoded-credentials` | Hardcoded API keys or passwords | `password: 'admin123'` in code | Move to environment variables |
| `xss-vulnerability` | Unsafe HTML rendering | `dangerouslySetInnerHTML` without sanitization | Sanitize with DOMPurify or use safe components |
| `missing-authentication` | Unprotected sensitive routes | Public access to admin endpoints | Add authentication check |
| `missing-authorization` | Authentication but no role check | Logged-in users can access other's data | Add authorization/permission validation |

### High Issues

| Rule | Pattern | Example | Fix |
|------|---------|---------|-----|
| `missing-input-validation` | No validation on user input | Direct use of query parameters | Validate/sanitize all user inputs |
| `insecure-dependency` | Using vulnerable dependency | Old version with known CVE | Update to patched version |
| `missing-csrf-protection` | No CSRF token validation | Form submission without CSRF check | Add CSRF middleware/validation |
| `unsafe-file-upload` | User files without restrictions | Accepting any file type/size | Validate file type, size, and scan for malware |
| `missing-rate-limiting-auth` | No rate limit on login attempts | Brute force possible on login | Implement rate limiting on auth endpoints |

### Medium Issues

| Rule | Pattern | Example | Fix |
|------|---------|---------|-----|
| `exposed-sensitive-data` | Logging sensitive information | `console.log(user)` including password | Remove sensitive data from logs |
| `weak-password-policy` | No password strength validation | Accepting any string as password | Enforce minimum complexity requirements |
| `missing-https-redirect` | Not enforcing HTTPS | HTTP requests allowed | Configure Next.js to enforce HTTPS |

---

## 8. Next.js & React Best Practices

### Critical Issues

| Rule | Pattern | Example | Fix |
|------|---------|---------|-----|
| `missing-suspense-boundary` | Long async operation without Suspense | Page with slow data fetch, no fallback | Wrap async component in Suspense with fallback |

### High Issues

| Rule | Pattern | Example | Fix |
|------|---------|---------|-----|
| `missing-error-component` | No error.tsx for error handling | Unhandled errors go to default page | Create error.tsx component |
| `missing-not-found` | No not-found.tsx for 404 | Invalid routes show server error | Create not-found.tsx component |
| `state-mutation` | Direct state mutation | `item.name = 'new'` instead of setState | Update immutably with new objects/arrays |

### Medium Issues

| Rule | Pattern | Example | Fix |
|------|---------|---------|-----|
| `missing-accessibility` | No alt text for images | `<img src="..." />` missing alt | Add descriptive alt text to all images |
| `missing-aria-labels` | No ARIA labels for interactive elements | `<button>` without accessible name | Add aria-label or text content |
| `missing-structured-data` | No schema.org markup | Missing structured data for SEO | Add JSON-LD for schema.org types |
| `hardcoded-text` | Untranslatable hardcoded strings | Text directly in JSX | Use i18n library for translations |

---

## 9. Testing & Documentation

### High Issues

| Rule | Pattern | Example | Fix |
|------|---------|---------|-----|
| `missing-unit-tests` | Critical function without tests | Utility without test file | Write unit tests for critical logic |
| `missing-api-documentation` | API route without documentation | Undocumented endpoint parameters | Document with JSDoc or OpenAPI |

### Medium Issues

| Rule | Pattern | Example | Fix |
|------|---------|---------|-----|
| `incomplete-test-coverage` | Tests missing for error paths | Only happy path tested | Add tests for error conditions |
| `missing-integration-tests` | No integration tests for workflows | Component interactions untested | Write integration/E2E tests |

---

## Severity Matrix

| Severity | Impact | Default Action |
|----------|--------|-----------------|
| **Critical** | Production crash, data loss, or security breach | Must fix before merge |
| **High** | Significant performance, architecture, or security issue | Must fix before production |
| **Medium** | Code quality or maintainability concern | Should fix in current sprint |
| **Low** | Minor style or documentation issue | Nice to have |
| **Info** | Suggestion or FYI | For consideration |

---

## How Rules Are Applied

1. **During Analysis**: Each changed file is scanned against relevant rules
2. **Categorization**: Issues grouped by severity and dimension
3. **Reporting**: Reported with context (file, line), description, and fix
4. **Quality Gate**: Counted against configurable thresholds
5. **Tracking**: Issues can be filtered by severity, dimension, or rule
