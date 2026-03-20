# Sample Next.js Review Report

## Summary
- PR: https://github.com/example/repo/pull/427
- Repository: example/repo
- Files Changed: 6 files, 210 additions, 35 deletions
- Issues Found: 1 Critical, 2 High, 3 Medium, 1 Low
- Quality Gate: FAIL

## Issues by Severity

### Critical
1. Hardcoded secret - src/lib/config.ts:42
- Description: API key is hardcoded in source.
- Impact: Credential exposure risk.
- Fix: Move key to environment variable.

### High
1. Missing auth check - src/app/api/admin/route.ts:18
- Description: Admin route has no auth guard.
- Impact: Unauthorized access risk.
- Fix: Add authentication and role verification middleware.

2. N+1 data fetch - src/app/dashboard/page.tsx:63
- Description: Loop performs one fetch per item.
- Impact: Severe latency under load.
- Fix: Batch requests or fetch aggregated data once.

## Notes
- Every issue includes exact file and line.
- Fix suggestions are shown when `output.suggestFixes=true`.
