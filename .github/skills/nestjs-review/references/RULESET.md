# NestJS Code Review - Detailed Ruleset

Complete specification of all review rules organized by dimension and severity level.

---

## 1. NestJS Conventions & Best Practices

### Critical Issues

| Rule | Pattern | Example | Fix |
|------|---------|---------|-----|
| `missing-injectable` | Service without @Injectable() | `export class UserService {}` | Add `@Injectable()` decorator |
| `invalid-module-exports` | Module not exporting providers | Module declares service but doesn't export | Export service in module.exports |
| `provider-without-decorator` | Provider without @Injectable/@Inject | `constructor(userService: UserService)` | Ensure UserService has @Injectable() |

### High Issues

| Rule | Pattern | Example | Fix |
|------|---------|---------|-----|
| `missing-dto` | No DTO for request body | `@Post() create(body: any)` | Create CreateUserDTO with class-validator |
| `missing-validation-pipe` | No validation decorators | DTO without @IsString, @IsEmail, etc. | Add validation decorators from class-validator |
| `business-logic-in-controller` | Complex logic in controller | Controller has database calls or calculations | Move to service layer |
| `async-without-await` | Async function not awaited | `this.userService.findOne()` (should be `await`) | Add await keyword |
| `missing-error-handler` | No error handling in async | `async method()` without try-catch | Add proper error handling |
| `direct-dependency-injection` | Hardcoded new instances | `new UserService()` instead of injected | Use dependency injection |

### Medium Issues

| Rule | Pattern | Example | Fix |
|------|---------|---------|-----|
| `inconsistent-naming` | Mixed naming conventions | Mix of camelCase and snake_case | Use consistent camelCase for methods, PascalCase for classes |
| `missing-return-type` | No return type annotation | `method() {}` instead of `method(): Promise<User>` | Add explicit return type |
| `unused-import` | Imported but not used | `import { something } from 'module'` but never used | Remove unused imports |
| `missing-lifecycle-hook` | Missing OnDestroy for cleanup | Class with event listeners but no OnDestroy | Implement OnDestroy and clean up listeners |

### Low Issues

| Rule | Pattern | Example | Fix |
|------|---------|---------|-----|
| `public-method-without-jsdoc` | Missing JSDoc on public methods | Public API method without documentation | Add /** */ JSDoc comment |
| `incomplete-jsdoc` | JSDoc missing @param/@returns | JSDoc without parameter descriptions | Document all parameters and return types |

---

## 2. Code Architecture & Structure

### Critical Issues

| Rule | Pattern | Example | Fix |
|------|---------|---------|-----|
| `circular-dependency` | Services depend on each other | UserService imports OrderService, OrderService imports UserService | Restructure into shared service or use forwardRef() |
| `missing-separation-of-concerns` | Business logic in repository | Repository with business calculations | Move calculations to service layer |
| `improper-use-of-middleware` | Middleware accessing business logic | Middleware making database calls | Use guards or interceptors for business logic |

### High Issues

| Rule | Pattern | Example | Fix |
|------|---------|---------|-----|
| `missing-repository-pattern` | Direct database access in service | `query().execute()` directly in service | Create repository class for data access |
| `service-without-interface` | Service without interface contract | `class UserService {}` without `interface IUserService` | Define interface for testability |
| `missing-guards-on-routes` | Unprotected endpoints | `@Get('admin')` without @UseGuards | Add authentication and authorization guards |
| `improper-exception-handling` | Throwing generic errors | `throw new Error()` | Use NestJS exceptions (BadRequestException, UnauthorizedException) |

### Medium Issues

| Rule | Pattern | Example | Fix |
|------|---------|---------|-----|
| `deep-module-nesting` | Unnecessary module hierarchy | Too many nested sub-modules | Flatten module structure unless necessary |
| `missing-repository-injection` | Repository not injected | Service accessing database directly | Inject repository into service |
| `hardcoded-values` | Magic numbers/strings | `const MAX_PAGE_SIZE = 100` in multiple places | Extract to constants file or config |

---

## 3. Complexity Issues

### High Issues

| Rule | Pattern | Threshold | Fix |
|------|---------|-----------|-----|
| `cyclomatic-complexity` | Too many branches | > 10 branches in single method | Break into smaller methods |
| `method-too-long` | Method exceeds size limit | > 50 lines in single method | Extract to helper methods |
| `nesting-depth` | Too deeply nested code | > 4 levels of nesting | Flatten logic with early returns |

### Medium Issues

| Rule | Pattern | Threshold | Fix |
|------|---------|-----------|-----|
| `cognitive-complexity` | Hard to understand code | Cognitive complexity > 15 | Simplify conditionals, extract methods |
| `class-too-complex` | Class has too many responsibilities | > 20 public methods | Split into multiple classes |
| `parameter-count` | Too many function parameters | > 4 parameters | Use object parameter or create DTO |

---

## 4. Performance Issues

### High Issues

| Rule | Pattern | Example | Fix |
|------|---------|---------|-----|
| `blocking-async` | Synchronous operation in async | `fs.readFileSync()` in async method | Use `fs.promises.readFile()` or async version |
| `no-pagination` | List endpoint without limit | `@Get() getAll()` returns all records | Add pagination (limit, offset, page) |
| `no-caching-strategy` | Frequently-called data not cached | Every request queries same data | Implement Redis caching with TTL |
| `memory-leak-listeners` | Event listeners not unsubscribed | `eventEmitter.on()` without `.off()` | Unsubscribe in OnDestroy lifecycle |

### Medium Issues

| Rule | Pattern | Example | Fix |
|------|---------|---------|-----|
| `inefficient-transformation` | Multiple loops over data | Loop to filter, then loop to map | Combine into single loop or use lodash |
| `large-object-allocation` | Large objects created in loops | `new Array(1000)` in loop | Allocate once outside loop |
| `string-concatenation-in-loop` | String += in loop | `for (let i = 0; i < n; i++) { str += item }` | Use array.join() or StringBuilder pattern |

---

## 5. SQL & Database Query Issues

### Critical Issues

| Rule | Pattern | Example | Fix |
|------|---------|---------|-----|
| `sql-injection` | String interpolation in SQL | `query(\`SELECT * FROM users WHERE id = ${id}\`)` | Use parameterized queries: `query('SELECT * FROM users WHERE id = ?', [id])` |
| `n-plus-one-query` | Loop with database calls | `for (user of users) { user.orders = findOrders(user.id) }` | Use JOIN or batch query: `find({relations: ['orders']})` |

### High Issues

| Rule | Pattern | Example | Fix |
|------|---------|---------|-----|
| `missing-eager-loading` | Related entities not loaded | Getting user, then separately getting their orders | Use eager loading: `find({relations: ['orders']})` or `.leftJoinAndSelect()` |
| `missing-select` | SELECT * instead of specific columns | `query('SELECT * FROM large_table')` | `SELECT id, name, email FROM large_table` |
| `missing-index` | Query on non-indexed column | WHERE clause on unindexed FK column | Add @Index() decorator or database index |
| `inefficient-join` | Multiple queries instead of join | Query users, then for each user query dept | Use single JOIN query |

### Medium Issues

| Rule | Pattern | Example | Fix |
|------|---------|---------|-----|
| `missing-transaction` | Multiple related queries not transactional | Create user, then create profile separately | Wrap in transaction to ensure atomicity |
| `missing-limit` | Unbounded query results | `find()` without limit | Add `.limit(pageSize)` |
| `hardcoded-limit` | Magic numbers in queries | `.limit(100)` hardcoded | Use config constant |

---

## 6. Database Schema Issues

### High Issues

| Rule | Pattern | Example | Fix |
|------|---------|---------|-----|
| `missing-constraint` | No database constraints | Column that should be unique isn't marked | Add @Unique() or UNIQUE constraint |
| `missing-not-null` | Optional column should be required | Email field allows NULL | Add @Column({ nullable: false }) or ALTER TABLE |
| `type-mismatch` | Database type doesn't match logic | Storing boolean as string | Fix column type (BOOLEAN not VARCHAR) |
| `missing-fk-constraint` | Foreign key relationship not defined | User has department_id but no FK constraint | Add FOREIGN KEY constraint |

### Medium Issues

| Rule | Pattern | Example | Fix |
|------|---------|---------|-----|
| `poor-naming` | Unclear column/table names | Column `x`, table `t1` | Use descriptive names: `user_id`, `users` |
| `denormalization-without-justification` | Denormalized data without comment | Storing user address in orders table | Document why denormalized or normalize |
| `missing-index` | Frequently-queried column unindexed | WHERE created_at > now() without index | Add @Index() on created_at |
| `bad-migration` | Migration file issues | Migration creates table after dropping | Ensure migrations are idempotent and safe |

---

## 7. Security Issues

### Critical Issues

| Rule | Pattern | Example | Fix |
|------|---------|---------|-----|
| `hardcoded-secret` | API keys, passwords in code | `const API_KEY = "sk-abc123"` | Use environment variables: `process.env.API_KEY` |
| `sql-injection` | (See Database Query Issues) | [See above] | Use parameterized queries |
| `authentication-bypass` | Unprotected sensitive endpoint | `@Get('/admin')` without @UseGuards(AuthGuard) | Add authentication guards |
| `authorization-bypass` | Missing authorization check | Anyone can access admin endpoint | Add role-based authorization |

### High Issues

| Rule | Pattern | Example | Fix |
|------|---------|---------|-----|
| `missing-input-validation` | No validation on user input | `@Post() create(@Body() body: any)` | Create DTO with validation decorators |
| `missing-sanitization` | Unsanitized string in response | Returning user input directly | Sanitize/escape output |
| `unsafe-file-upload` | No file type/size validation | `@Post('upload') upload(@UploadedFile() file)` | Validate file type, size, scan for malware |
| `missing-rate-limiting` | No rate limit on endpoints | API endpoint with no throttling | Add @Throttle() decorator |
| `missing-csrf-protection` | No CSRF tokens | State-changing operations unprotected | Add CSRF middleware or tokens |
| `weak-crypto` | Using weak hashing | `md5(password)` | Use bcrypt, argon2: `bcrypt.hash(password, 10)` |
| `exposed-stack-trace` | Error details in response | Returning full error stack to client | Handle errors, return generic messages |

### Medium Issues

| Rule | Pattern | Example | Fix |
|------|---------|---------|-----|
| `missing-https-check` | Not enforcing HTTPS | Allowing HTTP in production | Set `ENFORCE_HTTPS`, use middleware |
| `missing-helmet` | No security headers | No Helmet middleware configured | Add `app.use(helmet())` |
| `missing-cors-config` | CORS allowing all origins | `cors: '*'` or `cors: true` | Explicitly whitelist allowed origins |
| `debug-mode-enabled` | DEBUG logging in production | `app.use(morganDebug())` in prod | Disable debug logging in production |

---

## 8. SonarQube-Compatible Issues

### High Issues

| Rule | Category | Pattern | Fix |
|------|----------|---------|-----|
| `code-duplication` | Code Coverage | Same code in 3+ places | Extract to reusable function/service |
| `dead-code` | Code Coverage | Unused variable, function, import | Remove unused code |
| `type-unsafe-any` | Type Safety | `let x: any` | Replace `any` with specific type |
| `missing-type-annotation` | Type Safety | Function parameter without type | Add explicit type annotation |

### Medium Issues

| Rule | Category | Pattern | Fix |
|------|----------|---------|-----|
| `uncovered-branch` | Test Coverage | Branch not covered by tests | Add unit test for this branch |
| `low-test-coverage` | Test Coverage | Coverage < 70% on file | Increase test coverage |
| `unused-variable` | Code Quality | Variable assigned but never used | Remove or use the variable |

---

## 9. Additional Issues

### High Issues

| Rule | Category | Pattern | Fix |
|------|----------|---------|-----|
| `missing-try-catch` | Error Handling | Async operation without error handling | Add try-catch or .catch() |
| `silent-failure` | Error Handling | Exception caught but not logged | Log error or re-throw |
| `vulnerable-dependency` | Dependencies | Outdated package with known CVE | Update package: `npm update package@latest` |

### Medium Issues

| Rule | Category | Pattern | Example | Fix |
|------|----------|---------|---------|-----|
| `missing-logging` | Observability | Business-critical operation not logged | Database write without log | Add `this.logger.log('User created', userId)` |
| `insufficient-logging` | Observability | Error happens but not logged | `.catch(err => {})` without logging | Log error: `.catch(err => this.logger.error(err))` |
| `missing-tests` | Testing | No unit tests for service | Service not tested | Create user.service.spec.ts |
| `missing-integration-test` | Testing | Critical path not integration tested | Endpoint not tested end-to-end | Add e2e test |

---

## Severity Mapping Summary

| Severity | Typical Categories |
|----------|-------------------|
| **Critical** | Security vulnerabilities, data loss, crashes, SQL injection, unauth access |
| **High** | Arch violations, performance degradation, N+1 queries, missing validation |
| **Medium** | Code quality, complexity, maintenance, missing caching |
| **Low** | Naming, style, documentation |
| **Info** | Suggestions, FYI |

---

## Example Review Findings

### Issue #1: N+1 Query
```
Severity: Critical
Area: SQL & Database Queries
File: src/services/user.service.ts:42
Rule: n-plus-one-query

Description:
  In getUsers() method, you're making a database call for each user's orders:
  for (const user of users) {
    user.orders = await this.orderService.findByUserId(user.id);
  }
  
Impact:
  - If you fetch 1000 users, you make 1001 queries (1 for users + 1000 for orders)
  - With network latency, this turns a 50ms query into 50+ seconds

Fix:
  Use eager loading or batch query:
  const users = await this.userRepository.find({
    relations: ['orders']
  });
  
  Or use QueryBuilder:
  this.userRepository
    .createQueryBuilder('u')
    .leftJoinAndSelect('u.orders', 'o')
    .getMany();
```

### Issue #2: Missing Input Validation
```
Severity: High
Area: Security Issues
File: src/controllers/user.controller.ts:15
Rule: missing-input-validation

Description:
  @Post() create(@Body() body: any) { ... }
  
  No validation on request body. Attacker can pass any data structure.

Impact:
  - Invalid data reaches database
  - No type safety
  - Potential for injection attacks if data is mishandled

Fix:
  1. Create DTO:
     export class CreateUserDto {
       @IsString()
       name: string;
       
       @IsEmail()
       email: string;
     }
  
  2. Use in controller:
     @Post() create(@Body() body: CreateUserDto) { ... }
```

