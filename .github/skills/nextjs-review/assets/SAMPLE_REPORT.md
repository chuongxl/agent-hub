# 📋 Next.js Code Review Report - Sample

**PR**: https://github.com/example/commerce-web/pull/427
**Repository**: commerce-web
**Branch**: feature/admin-analytics
**Files Changed**: 11 files, +438 additions, -121 deletions
**Issues Found**: 15 total

---

## 🎯 Quality Gate Result

### **❌ FAIL**

**Reason**: 1 Critical issue found (threshold: 0 allowed)

| Metric | Value | Threshold | Status |
|--------|-------|-----------|--------|
| Critical Issues | **1** | 0 | ❌ FAIL |
| High Issues | 2 | 3 | ✅ PASS |
| Medium Issues | 7 | 10 | ✅ PASS |
| Low Issues | 5 | 20 | ✅ PASS |
| Avg Complexity | 8.9 | 10 | ✅ PASS |
| Test Coverage | 69% | 70% | ⚠️ MARGINAL |

---

## 🔴 Critical Issues (1 found)

### 1. Hardcoded Production Secret Exposed to Client Bundle
**Area**: Security Issues
**File**: `src/lib/config.ts:42`
**Severity**: 🔴 Critical
**Impact**: Credential leakage and account takeover risk

**Problem**:
```typescript
// ❌ SECRET IN SOURCE (client-accessible)
export const analyticsConfig = {
	provider: 'internal',
	apiKey: 'sk_live_9Kf2f...'
};
```

**Why this matters**:
- Any user can inspect bundled JavaScript and extract the key
- Stolen key can be used to call privileged analytics endpoints
- Violates basic secret management and compliance requirements

**Fix**:
```typescript
// ✅ Server-only env access
export const analyticsConfig = {
	provider: 'internal',
	apiKey: process.env.ANALYTICS_API_KEY ?? ''
};

if (!process.env.ANALYTICS_API_KEY) {
	throw new Error('ANALYTICS_API_KEY is required');
}
```

**Related Rules**:
- [hardcoded-credentials](../references/RULESET.md#critical-issues)

---

## 🟠 High Issues (2 found)

### 1. Missing Authorization Check on Admin API Route
**Area**: API Routes & Backend Issues
**File**: `src/app/api/admin/users/route.ts:18`
**Severity**: 🟠 High
**Impact**: Unauthorized users can access restricted admin data

**Problem**:
```typescript
// ❌ Authenticated user can access admin endpoint without role check
export async function GET() {
	const users = await db.user.findMany();
	return NextResponse.json({ users });
}
```

**Fix**:
```typescript
// ✅ Enforce role-based access
export async function GET(request: Request) {
	const session = await getServerSession(authOptions);
	if (!session || session.user.role !== 'admin') {
		return NextResponse.json({ error: 'Forbidden' }, { status: 403 });
	}

	const users = await db.user.findMany();
	return NextResponse.json({ users });
}
```

**Urgency**: High - fix before merge
**Related Rules**: [missing-authorization](../references/RULESET.md#critical-issues)

---

### 2. N+1 Query Pattern in Dashboard Page
**Area**: Database & Data Fetching
**File**: `src/app/dashboard/page.tsx:63`
**Severity**: 🟠 High
**Impact**: Significant latency increase for larger accounts

**Problem**:
```typescript
// ❌ 1 + N database calls
const stores = await db.store.findMany({ where: { ownerId } });

for (const store of stores) {
	store.orders = await db.order.findMany({
		where: { storeId: store.id }
	});
}
```

**Metrics**:
- For 80 stores: 81 DB queries instead of 1-2
- p95 dashboard load estimated 240ms -> 2.8s

**Fix**:
```typescript
// ✅ Use relation include to fetch in one query
const stores = await db.store.findMany({
	where: { ownerId },
	include: {
		orders: {
			select: { id: true, total: true, createdAt: true }
		}
	}
});
```

**Urgency**: High - fix before production deploy
**Related Rules**: [n-plus-one-queries](../references/RULESET.md#high-issues)

---

## 🟡 Medium Issues (7 found)

### 1. Missing Suspense Boundary Around Slow Async Block
**Area**: Next.js & React Best Practices
**File**: `src/app/dashboard/page.tsx:24`
**Severity**: 🟡 Medium
**Impact**: Perceived slowness and blank rendering during async fetch
**Suggestion**: Wrap async-heavy section in `<Suspense fallback={<DashboardSkeleton />}>...`.

### 2. Component Exceeds Recommended Size
**Area**: Complexity Issues
**File**: `src/components/AdminAnalyticsPanel.tsx:1`
**Severity**: 🟡 Medium
**Impact**: Harder testing and maintainability (322 lines > 300 threshold)
**Suggestion**: Extract chart widgets and table controls into child components.

### 3. Missing Error Handling in API Route
**Area**: API Routes & Backend Issues
**File**: `src/app/api/reports/route.ts:31`
**Severity**: 🟡 Medium
**Impact**: Uncaught exceptions return generic 500 without logs
**Suggestion**: Add `try/catch`, structured logging, and explicit status code mapping.

### 4. Metadata Not Defined for Admin Reports Page
**Area**: Next.js Conventions & Best Practices
**File**: `src/app/admin/reports/page.tsx:1`
**Severity**: 🟡 Medium
**Impact**: Reduced SEO/share metadata quality
**Suggestion**: Export `metadata` with title, description, and robots directives.

### 5. Inconsistent Response Schema Across Report Endpoints
**Area**: API Routes & Backend Issues
**File**: `src/app/api/reports/summary/route.ts:44`
**Severity**: 🟡 Medium
**Impact**: Frontend parsing complexity and type drift risk
**Suggestion**: Standardize on `{ data, error, meta }` envelope.

### 6. Weak Typing via `any` in Chart Data Mapper
**Area**: Code Architecture & Structure
**File**: `src/lib/chartMapper.ts:12`
**Severity**: 🟡 Medium
**Impact**: Runtime type errors and reduced editor safety
**Suggestion**: Replace `any` with `AnalyticsSeries` and `AnalyticsPoint` interfaces.

### 7. No Request Validation for Report Filter Input
**Area**: Security Issues
**File**: `src/app/api/reports/route.ts:17`
**Severity**: 🟡 Medium
**Impact**: Invalid payloads can trigger expensive or unexpected queries
**Suggestion**: Validate request body with zod schema before query execution.

---

## 🔵 Low Issues (5 found)

1. Missing alt text on decorative analytics image
	 - Area: Next.js & React Best Practices
	 - File: `src/components/HeroAnalytics.tsx:55`

2. Inline anonymous callback recreated each render
	 - Area: Performance Issues
	 - File: `src/components/DateRangeFilter.tsx:71`

3. TODO comment without issue reference
	 - Area: Additional Issues
	 - File: `src/lib/exportCsv.ts:19`

4. Inconsistent naming (`sales_total` vs `salesTotal`)
	 - Area: Next.js Conventions & Best Practices
	 - File: `src/lib/formatters.ts:38`

5. Missing JSDoc for public analytics utility
	 - Area: Additional Issues
	 - File: `src/lib/metrics.ts:1`

---

## 📊 Analysis by Dimension

1. Next.js Conventions & Best Practices: ⚠️ Issues (3 findings)
2. Code Architecture & Structure: ⚠️ Issues (2 findings)
3. Complexity Issues: ⚠️ Issues (1 finding)
4. Performance Issues: ⚠️ Issues (2 findings)
5. API Routes & Backend Issues: ⚠️ Issues (3 findings)
6. Database & Data Fetching: ❌ Critical (1 high-impact issue)
7. Security Issues: ❌ Critical (1 critical, 1 medium)
8. Next.js & React Best Practices: ⚠️ Issues (2 findings)
9. Additional Issues: ⚠️ Issues (2 findings)

---

## ✅ Recommendations

### Before Merge
1. Remove hardcoded secret and rotate leaked key.
2. Add role-based authorization guard to admin users route.

### Before Production
1. Refactor dashboard data loading to eliminate N+1 query pattern.
2. Add request validation and robust error handling in reporting APIs.

### Next Sprint
1. Break large analytics panel component into feature-focused subcomponents.
2. Raise test coverage from 69% to at least 75% for reporting modules.

---

## 📌 Next Steps
- [ ] Fix all Critical issues before merge
- [ ] Re-run review and confirm quality gate PASS
- [ ] Add regression tests for admin authorization path
- [ ] Open follow-up tickets for remaining Medium/Low findings
