# Next.js Code Review Skill - FAQ

Frequently asked questions about using the Next.js Code Review skill.

---

## Getting Started

### Q: How do I run a code review?

**A:** Provide either a PR link, PR number, or branch name:

```
# Full PR link (recommended)
@nextjs-review https://github.com/myorg/app/pull/427

# PR number (from within repo)
@nextjs-review pr:427

# Branch name
@nextjs-review feature/my-changes
```

The skill will automatically:
1. Validate the input
2. Clone/fetch the repository
3. Extract the source and destination branches
4. Perform comprehensive analysis
5. Generate a severity-ranked report
6. Determine quality gate pass/fail

### Q: What repositories can I review?

**A:** Any Next.js project on:
- GitHub ✓
- GitLab ✓
- Bitbucket ✓

**Requirements**:
- Repository must be accessible (public or authenticated with token)
- Must have `next` in package.json dependencies
- Must have `app/` (App Router) or `pages/` directory

### Q: Do I need to install anything?

**A:** No! The skill is fully cloud-based. Just:

1. Provide a PR link or branch name
2. (Optional) Set auth token for private repos
3. The skill handles everything else

### Q: How long does a review take?

**A:** Typically:
- **5-15 files**: 15-20 seconds
- **15-30 files**: 20-30 seconds  
- **30-50 files**: 30-45 seconds
- **50+ files**: 45-60 seconds

Large repositories may take longer due to git operations.

---

## Input & Authentication

### Q: What's the difference between PR link, PR number, and branch name?

**A:**

| Input | Example | Requirements |
|-------|---------|--------------|
| PR Link | `https://github.com/org/repo/pull/427` | No local context needed |
| PR Number | `pr:427` or `427` | Must be in repo directory |
| Branch Name | `feature/auth-system` | Must be in repo directory |

**Recommendation**: Use PR links for the most reliable results.

### Q: How do I set up authentication for private repositories?

**A:**

```bash
# GitHub
export GITHUB_TOKEN=ghp_xxxxxxxxxxxxxxxxxxxx

# GitLab
export GITLAB_TOKEN=glpat_xxxxxxxxxxxxxxxxxxxx

# Bitbucket
export BB_TOKEN=xxxxxxxxxxxxxxxxxxxx
```

**Token Permissions**:
- **GitHub**: repo (full), read:repo_hook
- **GitLab**: api, read_repository
- **Bitbucket**: repository:read, pullrequest:read

### Q: Can the skill access private repositories without a token?

**A:** Yes, if:
1. The repository clone URL uses SSH authentication
2. Your SSH key is already configured for the hosting platform
3. You're running the skill locally with SSH agent available

For remote execution, use environment variable tokens.

### Q: What happens to my authentication credentials?

**A:**
- Credentials are **never logged**
- Only used for API calls to fetch metadata
- **Never included** in review reports
- Tokens can be temporary/revoked after review

---

## Review Findings

### Q: What does "Quality Gate" mean?

**A:** The Quality Gate is a pass/fail decision for the PR:

| Result | Criteria |
|--------|----------|
| ✅ **PASS** | No CRITICAL findings, ≤3 HIGH findings |
| ⚠️ **WARN** | 1-2 CRITICAL findings, ≤5 HIGH findings |
| ❌ **FAIL** | 3+ CRITICAL findings OR 6+ HIGH findings |

**Note**: Quality Gate is informational, not a hard block. Your team decides whether to merge despite warnings.

### Q: What's the difference between CRITICAL and HIGH severity?

**A:**

| Severity | Examples | Action |
|----------|----------|--------|
| **CRITICAL** | Hardcoded secrets, SQL injection, security vulnerabilities | Must fix before merging |
| **HIGH** | N+1 queries, major performance issues, unreliable patterns | Should fix in this PR |
| **MEDIUM** | Code quality, maintainability, style issues | Consider fixing |
| **LOW** | Minor style, suggestions, best practices | Nice to have |
| **INFO** | Informational tips, patterns | FYI |

### Q: How are findings prioritized?

**A:** By severity tier, then by:
1. **Impact** - Affects more code or users
2. **Effort** - Easier issues first
3. **Risk** - Higher risk issues listed first

This helps reviewers focus on what matters most.

### Q: Can I ignore certain finding types?

**A:** Yes, with `.nextjs-reviewignore`:

```bash
# .nextjs-reviewignore
# Ignore specific issues in entire codebase
perf/image-optimization
style/naming-convention

# Ignore issues in specific files
security/hardcoded-secret: src/config.ts
```

Then run:
```
@nextjs-review https://...  --ignore-file=.nextjs-reviewignore
```

### Q: Why does it flag my code if it works?

**A:** The skill focuses on:
- **Code quality** - Is it maintainable long-term?
- **Security** - Are there vulnerabilities?
- **Performance** - Will it scale?
- **Best practices** - Does it follow Next.js patterns?

Code can "work" but have issues that cause problems later (security breaches, slow performance, bugs when scaled).

---

## Next.js Specific

### Q: What are Server vs Client Components?

**A:** 
```typescript
// Server Component (default in app/ directory)
export default function Dashboard() {
  const data = await fetchFromDB();
  return <div>{data}</div>;
}

// Client Component (requires "use client")
"use client";
import { useState } from "react";
export default function Counter() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

**The skill checks**:
- ✓ Server Components for data fetching
- ✓ Client boundaries are clear
- ✗ Mixing fetching in Client Components (bad)
- ✗ Unnecessary "use client" directives

### Q: Why does it warn about importing Client Components in Server Components?

**A:** Actually, the skill allows this! You **can** import Client Components in Server Components:

```typescript
// ✅ OK
import ClientButton from './ClientButton'; // has "use client"

export default function ServerPage() {
  return <ClientButton />;
}
```

But the skill warns if:
- ✗ ClientComponent imports data-fetching only functions
- ✗ No clear Server/Client boundary

### Q: What's the App Router vs Pages Router?

**A:**

| Feature | App Router | Pages Router |
|---------|-----------|-------------|
| Directory | `app/` | `pages/` |
| Modern | ✓ Recommended | ⚠️ Legacy |
| Route Handler | `route.ts` | `pages/api/` |
| Server Components | ✓ Yes | ✗ No |
| Layout | ✓ Built-in | ✗ Manual HOC |

**The skill**:
- Prefers App Router (modern)
- Supports both
- Flags mixing both in same project

### Q: What does "RSC" mean?

**A:** RSC = React Server Components

- **Server Component**: Runs on server, can fetch data, no browser APIs
- **Client Component**: Runs in browser, uses hooks, interactive

The skill verifies you're using the right type for each component.

---

## Performance Analysis

### Q: Why does it flag missing `alt` attributes?

**A:** It doesn't directly, but it may flag accessibility issues if using `<img>` instead of Next.js `<Image>`:

```typescript
// ❌ Bad - missing Next.js optimization
<img src="photo.jpg" alt="descriptive" />

// ✅ Good - Next.js optimized
import Image from 'next/image';
<Image src="photo.jpg" alt="descriptive" width={400} height={300} />
```

The skill recommends:
- Use Next.js Image component
- Provide width/height props
- Enable responsive images

### Q: What's a "dependency array" issue?

**A:** Appears in hooks like `useEffect`:

```typescript
// ❌ Bad - missing dependency
useEffect(() => {
  console.log(userId); // <- used but not in dependencies
}, []); // <- missing userId

// ✅ Good
useEffect(() => {
  console.log(userId);
}, [userId]);
```

Missing dependencies cause **stale closures** and bugs.

### Q: Why does it care about bundle size?

**A:**
- Larger bundles = slower page loads
- Each 100KB adds ~0.5s to load time
- Slower pages = lower SEO, less engagement

The skill checks:
- Large components (should split)
- Unused dependencies
- Heavy libraries (suggest alternatives)
- Missing code splitting (`dynamic()`)

---

## Security

### Q: What's a "hardcoded secret"?

**A:** API keys, tokens, or passwords in code:

```typescript
// ❌ Bad - hardcoded token
const API_KEY = "sk_live_1234567890";
fetch(`https://api.example.com?key=${API_KEY}`);

// ✅ Good - from environment
const API_KEY = process.env.API_KEY;
fetch(`https://api.example.com?key=${API_KEY}`);
```

**Never hardcode**:
- API keys
- Database passwords
- Authentication tokens
- Private keys
- Encryption keys

Use environment variables instead.

### Q: What's SQL injection?

**A:** Concatenating user input into SQL queries:

```typescript
// ❌ Bad - SQL injection vulnerability
const userId = req.query.id; // untrusted input
const query = `SELECT * FROM users WHERE id = ${userId}`;
db.query(query);

// Attacker could pass: id = "1 OR 1=1" -- 
// This would return all users!

// ✅ Good - parameterized query
const userId = req.query.id;
const query = `SELECT * FROM users WHERE id = ?`;
db.query(query, [userId]);
```

Parameterized queries safely handle user input.

### Q: What about XSS (Cross-Site Scripting)?

**A:** Injecting user input into HTML:

```typescript
// ❌ Bad - XSS vulnerability
const message = req.query.msg;
return <div>{dangerouslySetInnerHTML={{ __html: message }}}</div>;

// Attacker could pass: msg = "<img src=x onerror='alert(\"hacked\")'>"

// ✅ Good - React escapes by default
const message = req.query.msg;
return <div>{message}</div>; // Safe!
```

Next.js/React escapes HTML by default, but `dangerouslySetInnerHTML` bypasses that.

### Q: How do I handle environment variables securely?

**A:**

```typescript
// ✅ Correct approach
// .env.local (git-ignored)
API_SECRET=sk_live_secret

// next.config.js
module.exports = {
  env: {
    NEXT_PUBLIC_API_URL: process.env.NEXT_PUBLIC_API_URL,
  },
};

// pages/api/protected.ts
export default function handler(req, res) {
  const secret = process.env.API_SECRET; // Only on server
  // Safe - not exposed to client
}

// pages/index.tsx
const publicUrl = process.env.NEXT_PUBLIC_API_URL; // OK on client
// Only public data via NEXT_PUBLIC_ prefix
```

**Rules**:
- `NEXT_PUBLIC_*` → Browser visible, use for public data only
- Regular vars → Server-only, safe for secrets
- Never `NEXT_PUBLIC_SECRET_KEY` ❌

---

## Database & Queries

### Q: What is an N+1 query?

**A:** Loop of database queries instead of single batch:

```typescript
// ❌ Bad - N+1: 1 query for posts + N queries for authors
const posts = await db.post.findMany();
for (const post of posts) {
  post.author = await db.user.findUnique({ where: { id: post.authorId } });
}

// ✅ Good - 1 query using relations
const posts = await db.post.findMany({
  include: { author: true },
});
```

N+1 queries:
- Slow with large datasets
- Hammer the database
- Often unintentional

The skill detects loop + DB patterns.

### Q: Why does the skill check my SQL formatting?

**A:** Reviewable SQL:
- Easy to audit for security
- Easy to optimize
- Easy for team to maintain

```sql
-- ✅ Good - readable
SELECT u.id, u.email, COUNT(p.id) as post_count
FROM users u
LEFT JOIN posts p ON u.id = p.author_id
WHERE u.created_at > '2024-01-01'
GROUP BY u.id
ORDER BY post_count DESC;

-- ❌ Bad - hard to review
SELECT u.id,u.email,COUNT(p.id)FROM users u LEFT JOIN posts p ON u.id=p.author_id WHERE u.created_at>'2024-01-01'GROUP BY u.id ORDER BY COUNT(p.id)DESC;
```

### Q: What ORM patterns does the skill validate?

**A:** For **Prisma**:
- ✓ Using `select()` for column projection
- ✗ Over-fetching all columns
- ✓ Using `include()` for relations
- ✗ Fetching relations in loops (N+1)

For **Drizzle**:
- ✓ Using proper type inference
- ✓ Avoiding raw SQL when possible
- ✗ Missing parameterization

---

## Interpreting Reports

### Q: What information is in the review report?

**A:** A comprehensive report includes:

1. **Executive Summary**
   - Overall quality score (0-100)
   - Quality Gate result
   - Key findings at a glance

2. **Findings by Severity**
   - CRITICAL findings first
   - Then HIGH, MEDIUM, LOW, INFO
   - Each with: code snippet, explanation, fix suggestion

3. **Detailed Metrics**
   - Files analyzed
   - Lines changed
   - Complexity statistics
   - Coverage metrics

4. **Recommendations**
   - Top 3 things to fix
   - Best practices applied
   - Patterns to follow

### Q: How do I use the report for team feedback?

**A:**

```markdown
## Things to address before merge

**CRITICAL (Must fix)**
- [ ] Remove hardcoded API key in config.ts:16
- [ ] Parameterize SQL query in lib/db.ts:42

**HIGH (Should fix)**
- [ ] Add missing useEffect dependencies in components/Chart.tsx
- [ ] Refactor complex function (complexity: 12) in utils/parser.ts

**MEDIUM (Nice to have)**
- [ ] Improve image optimization in components/Hero.tsx
- [ ] Add missing JSDoc comments

---

## What went well
✅ Good Server Component patterns
✅ No security vulnerabilities detected  
✅ Clean code organization
```

Share these in PR comments or discussions.

### Q: Can I export the report to other formats?

**A:** The skill supports:
- **Markdown** - For PR comments, emails
- **HTML** - For viewing in browser
- **JSON** - For CI/CD integration

Request specific format:
```
@nextjs-review https://... --format=html --output=./report.html
```

---

## Troubleshooting

### Q: "Not a Next.js project" error

**A:** Verify:
1. `package.json` exists: `cat package.json | grep -i next`
2. `app/` or `pages/` directory exists: `ls -d app/ pages/`
3. `next` is a dependency: `npm list next`

**Fix**:
```bash
npm install next
```

### Q: "Branch not found" error

**A:** Branch name is wrong or doesn't exist:

```bash
# List branches
git branch -r | grep <search>

# Fetch latest
git fetch origin

# Try again with exact name
@nextjs-review feature/exact-branch-name
```

### Q: "API rate limit exceeded"

**A:** Too many reviews in short time:

- GitHub: 60 requests/hour (public), 5000/hour (authenticated)
- Wait 5-15 minutes
- Or set GITHUB_TOKEN for higher limit

### Q: "Cannot access repository"

**A:** Repository is private or doesn't exist:

- ✓ Set authentication token
- ✓ Verify repo URL is correct
- ✓ Check token has `repo` permission
- ✓ For SSH: Verify SSH key is configured

### Q: Review result seems wrong

**A:** Common reasons:

1. **Analyzing wrong branch**
   - Verify source/destination branches in report
   - Ensure branch is up-to-date

2. **Analyzing wrong files**
   - Check changed files list in report
   - Run `git diff` manually to compare

3. **Missing recent changes**
   - Git may be cached
   - Try: `@nextjs-review <branch> --refresh`

4. **Configuration not applied**
   - Check `next.config.js`, `tsconfig.json`
   - Some settings require restart

---

## Advanced Usage

### Q: Can I run reviews in CI/CD?

**A:** Yes! In GitHub Actions:

```yaml
name: Code Review
on: [pull_request]

jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Next.js Code Review
        run: |
          nextjs-review \
            --repo=${{ github.repository }} \
            --pr=${{ github.event.pull_request.number }} \
            --token=${{ secrets.GITHUB_TOKEN }} \
            --format=json > report.json
      - name: Comment on PR
        run: |
          # Parse report and add comment
```

### Q: How do I customize severity rules?

**A:** Create `.nextjs-review.json`:

```json
{
  "rules": {
    "perf/image-optimization": {
      "severity": "HIGH",
      "enabled": true
    },
    "style/naming-convention": {
      "severity": "LOW",
      "enabled": false
    }
  },
  "ignorePatterns": [
    "**/*.test.ts",
    "dist/**"
  ]
}
```

### Q: Can I exclude certain files?

**A:** Yes, with `.nextjs-reviewignore` or config:

```
# .nextjs-reviewignore
dist/**
node_modules/**
*.generated.ts
.next/**
```

Or in `.nextjs-review.json`:

```json
{
  "ignoreFiles": [
    "dist/**",
    "**/*.generated.ts"
  ]
}
```

### Q: How do I set up team standards?

**A:** Create shared `.nextjs-review.json` in repo:

```json
{
  "team": "platform",
  "standards": {
    "minCoverage": 80,
    "maxComplexity": 10,
    "requireJSDoc": true,
    "requireTests": true
  },
  "rules": {
    "security/*": { "severity": "CRITICAL" },
    "perf/*": { "severity": "HIGH" },
    "style/*": { "severity": "LOW" }
  }
}
```

Commit to repo, everyone gets same standards.

---

## Performance Tips

### Q: How can I make reviews faster?

**A:**

1. **Use PR links** (pre-fetches metadata)
   ```
   @nextjs-review https://github.com/org/repo/pull/427
   # vs
   @nextjs-review feature/my-changes  # slower
   ```

2. **Review smaller PRs**
   - 5-10 files: ~10s
   - 50+ files: ~45s
   
3. **Run locally** for continuous feedback
   ```bash
   npx nextjs-review --watch feature/my-changes
   ```

4. **Cache results**
   ```bash
   # Results cached for 1 hour per branch
   @nextjs-review feature/my-changes --use-cache
   ```

### Q: Can I review multiple PRs?

**A:** Yes, serially:

```bash
nextjs-review pr:427 pr:428 pr:429
```

Or compare stats:

```bash
nextjs-review --compare=pr:427 pr:428
# Shows: what changed between PR quality
```

---

## Getting Help

### Q: Where do I report bugs?

**A:**
- GitHub Issues: https://github.com/nextjs-review/issues
- Provide: PR link, error message, expected behavior

### Q: How do I request features?

**A:**
- GitHub Discussions: https://github.com/nextjs-review/discussions  
- Describe: Use case, why needed, examples

### Q: How do I stay updated?

**A:**
- Follow GitHub releases
- Subscribe to security advisories
- Join community Slack/Discord

---

## Quick Reference

### Common Commands
```bash
# Review PR
@nextjs-review https://github.com/org/repo/pull/427

# Review branch
@nextjs-review feature/my-changes

# Review from CI
nextjs-review --token=$GITHUB_TOKEN --format=json

# With custom config
@nextjs-review https://... --config=.nextjs-review.json

# Export HTML report
@nextjs-review https://... --format=html --output=report.html
```

### Severity Meanings
- 🔴 **CRITICAL**: Must fix before merging
- 🟠 **HIGH**: Should fix in this PR
- 🟡 **MEDIUM**: Consider fixing
- 🔵 **LOW**: Nice to have
- ⚪ **INFO**: FYI

### Key Files
- `package.json` → Dependencies, Next.js version
- `tsconfig.json` → TypeScript configuration
- `next.config.js` → Next.js settings
- `app/` → App Router (modern)
- `pages/` → Pages Router (legacy)
- `.env.local` → Local environment variables (git-ignored)
- `.env.example` → Template for env vars

---

**Last Updated**: 2024
**Version**: 1.0  
**For Help**: See documentation or file an issue on GitHub
