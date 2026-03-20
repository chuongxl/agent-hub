# Java Review Rules - Complete Ruleset

This document contains 100+ review rules organized by dimension and severity for Java code review analysis.

---

## 1. Java Conventions & Best Practices (20+ rules)

### Naming Conventions

**Rule: PascalCase Class Names** | Severity: Medium
- Classes must be PascalCase (MyService, UserController, etc.)
- Check: `class [A-Z][a-zA-Z0-9]*`
- Fix: Rename class to follow convention

**Rule: CamelCase Method Names** | Severity: Low
- Public methods must be camelCase (getUserById, calculateTotal, etc.)
- Check: `public [a-z][a-zA-Z0-9_]*\(`
- Fix: Rename method to follow convention

**Rule: Constant Naming** | Severity: Low
- Static final fields must be UPPER_CASE (MAX_SIZE, DEFAULT_VALUE, etc.)
- Check: `static final.*[a-z]`
- Fix: Rename constant to UPPER_CASE

**Rule: Package Naming** | Severity: Low
- Packages must be lowercase.hyphenated (com.acme.service, org.app.util, etc.)
- Fix: Reorganize package structure

---

### Code Organization

**Rule: File Extends Threshold** | Severity: Medium
- Java files should not exceed 400 lines
- Check: File line count
- Fix: Split into multiple files

**Rule: Class Member Organization** | Severity: Low
- Order: static fields → instance fields → constructors → methods → inner classes
- Fix: Reorganize members

**Rule: Unused Imports** | Severity: Low
- Remove all unused import statements
- Check: compare imports to actual usage
- Fix: Remove unused imports with IDE cleanup

---

### JavaDoc & Comments

**Rule: Public API JavaDoc** | Severity: Medium
- All public classes, methods, fields must have JavaDoc
- Exception: getters/setters with obvious names
- Check: `/\*\*.*\*/` above public members
- Fix: Add JavaDoc comments

**Rule: Constructor Documentation** | Severity: Low
- All constructors must document parameters
- Check: `@param` tags in JavaDoc
- Fix: Add parameter documentation

**Rule: Method Return Documentation** | Severity: Low
- Methods returning non-void must document return value
- Check: `@return` tag in JavaDoc
- Fix: Add return documentation

---

## 2. Code Architecture & Structure (18+ rules)

### Design Patterns

**Rule: Dependency Injection** | Severity: High
- Use constructor injection, not field injection (@Autowired on fields is anti-pattern)
- Check: `@Autowired` on private fields
- Fix: Move to constructor parameter and inject

**Rule: Proper Use of Design Patterns** | Severity: Medium
- Prefer established patterns: Factory, Strategy, Observer, Builder, etc.
- Avoid: Utility class abuse, God objects
- Fix: Refactor to appropriate pattern

**Rule: Interface Segregation** | Severity: Medium
- Interfaces should be small and focused
- Flag: Interfaces with >5 public methods
- Fix: Split into smaller, focused interfaces

---

### Layering & Separation of Concerns

**Rule: Service Layer Responsibility** | Severity: High
- Business logic belongs in Service (@Service), not Controller
- Flag: Complex logic in @Controller or @RestController
- Fix: Extract to Service class

**Rule: Repository Isolation** | Severity: High
- Data access only in Repository (@Repository), not Service
- Flag: Direct database calls or JDBC in Service
- Fix: Move to Repository method

**Rule: Controller Simplicity** | Severity: Medium
- Controllers should only handle HTTP concerns
- Flag: Business logic, data transformation, or Hibernate-awareness
- Fix: Move to Service layer

---

### SOLID Principles

**Rule: Single Responsibility** | Severity: High
- Each class should have one reason to change
- Flag: Classes with >20 public methods
- Flag: Classes doing multiple things (e.g., both HTTP and data access)
- Fix: Split into single-purpose classes

**Rule: Open/Closed Principle** | Severity: Medium
- Classes should be open for extension, closed for modification
- Flag: Large if-else chains for different types
- Flag: Direct class instantiation instead of polymorphism
- Fix: Use inheritance/interfaces for extensibility

**Rule: Liskov Substitution** | Severity: High
- Derived classes must be substitutable for base classes
- Flag: Overridden methods with incompatible signatures
- Flag: Exceptions thrown by override not in throws clause
- Fix: Ensure contracts are maintained

---

## 3. Complexity Issues (15+ rules)

### Method Complexity

**Rule: Cyclomatic Complexity** | Severity: High
- Methods should have cyclomatic complexity ≤ 10
- Check: Count decision points (if, for, while, switch case, catch)
- Flag: Methods with >10 complexity
- Fix: Extract helper methods, reduce conditional branches

**Rule: Method Length** | Severity: Medium
- Methods should not exceed 50 lines
- Check: Line count per method (excluding braces)
- Flag: Methods >50 lines
- Fix: Extract helper methods

**Rule: Parameter Count** | Severity: Medium
- Methods should have ≤ 5 parameters
- Flag: Methods with >5 parameters
- Fix: Use object as parameter container (DTO) or break into smaller methods

---

### Class Complexity

**Rule: Class Size** | Severity: Medium
- Classes should not exceed 500 lines
- Check: Line count per class
- Flag: Classes >500 lines
- Fix: Split into smaller, focused classes

**Rule: Public Method Count** | Severity: Medium
- Classes should have ≤ 20 public methods
- Flag: >20 public methods
- Fix: Split into multiple classes or reduce API surface

**Rule: Nesting Depth** | Severity: Medium
- Avoid nesting >4 levels deep (if/for nested)
- Flag: Code with >4 nesting levels
- Fix: Extract helper methods to reduce nesting

---

### Cognitive Complexity

**Rule: Cognitive Complexity** | Severity: High
- Cognitive complexity should be ≤ 15
- Check: Nested conditions, loops, and switch statements
- Higher weight for: nested ternary, switch with nested logic
- Flag: Methods with >15 cognitive complexity
- Fix: Simplify logic, extract helper methods

---

## 4. Performance Issues (18+ rules)

### Algorithm & Data Structure

**Rule: String Concatenation in Loops** | Severity: High
- Use StringBuilder for string concatenation in loops
- Flag: `+` operator inside loops
- Fix: Use `StringBuilder.append()`

**Rule: Collection Initialization** | Severity: Medium
- Initialize collections with appropriate size
- Flag: `new HashMap()` without size, repeated add() calls
- Fix: `new HashMap<>(expectedSize)` with initial capacity

**Rule: Inefficient Collection Operations** | Severity: Medium
- Avoid `O(n)` operations (contains, indexOf) inside loops
- Flag: List.contains() or indexOf() inside nested loops
- Fix: Use HashSet for membership checks

---

### Database & I/O

**Rule: N+1 Query Pattern** | Severity: High
- Avoid loading related objects one-by-one
- Flag: Loop over entities then load related data per entity
- Fix: Use JOIN FETCH or batch loading

**Rule: Missing Prepared Statements** | Severity: Critical
- Always use PreparedStatement, never string concatenation for SQL
- Flag: String concatenation in SQL queries
- Fix: Use `?` placeholders and setXXX() methods

**Rule: Eager Loading** | Severity: High
- Load related objects eagerly to avoid N+1
- Flag: Sets marked as LAZY without explicit FETCH in queries
- Fix: Use JOIN FETCH or @Fetch(FetchMode.JOIN)

---

### Memory Management

**Rule: Unclosed Resources** | Severity: Critical
- Close all resources: Connection, Statement, ResultSet, File, Stream
- Flag: Called but never closed
- Fix: Use try-with-resources or explicit finally block

**Rule: Event Listener Registration** | Severity: High
- Unregister listeners to prevent memory leaks
- Flag: Listeners registered but never unregistered
- Fix: Unregister in cleanup methods or use weak references

**Rule: Large Object Allocation** | Severity: Medium
- Avoid creating large objects in loops
- Flag: New byte[16000000] or similar inside loops
- Fix: Create once before loop, reuse

---

## 5. SQL & Database Query Issues (15+ rules)

**Rule: SELECT * Queries** | Severity: Medium
- Select only needed columns
- Flag: `SELECT *` in queries
- Fix: Explicitly list required columns: `SELECT id, name, email FROM user`

**Rule: Missing Indexes** | Severity: High
- Add indexes on frequently queried columns
- Flag: Foreign key columns without indexes
- Flag: WHERE clause columns without indexes
- Fix: Add @Index or create database index

**Rule: Join Efficiency** | Severity: High
- Use single JOIN instead of multiple queries
- Flag: Processing results in application instead of JOIN
- Fix: Use INNER JOIN with appropriate join condition

**Rule: Query Caching** | Severity: Medium
- Cache frequently executed queries
- Flag: Same query executed repeatedly
- Fix: Use @Cacheable or Hibernate second-level cache

---

## 6. Database Schema Issues (12+ rules)

**Rule: Missing Primary Key** | Severity: Critical
- All tables must have primary key
- Fix: Add @Id to entity identifier

**Rule: Missing Constraints** | Severity: High
- Use NOT NULL, UNIQUE, FOREIGN KEY constraints
- Fix: Add @Column(nullable = false), @Unique, @JoinColumn

**Rule: Bad Column Names** | Severity: Low
- Use snake_case for database columns
- Flag: camelCase or mixed case column names
- Fix: Use @Column(name = "correct_name")

---

## 7. Security Issues (25+ rules)

### Input Validation

**Rule: Missing Input Validation** | Severity: Critical
- Validate all user inputs
- Flag: Parameters directly used without validation
- Fix: Use @Valid @RequestBody or explicit validation

**Rule: SQL Injection Risk** | Severity: Critical
- Use parameterized queries, never concatenate
- Flag: String concatenation in SQL
- Fix: Use PreparedStatement or query methods with parameters

**Rule: Command Injection** | Severity: Critical
- Never execute user input as system commands
- Flag: Runtime.exec() with user input
- Fix: Use ProcessBuilder with array of safe parameters

---

### Secrets & Credentials

**Rule: Hardcoded Secrets** | Severity: Critical
- Never hardcode passwords, keys, tokens
- Flag: Strings containing "password", "secret", "key", "token"
- Fix: Load from secure configuration (environment, vault)

**Rule: API Key Exposure** | Severity: Critical
- Never log, expose, or commit API keys
- Flag: API keys in code
- Fix: Use environment variables or secure vaults

---

### Authentication & Authorization

**Rule: Missing @Secured/@PreAuthorize** | Severity: High
- Protect sensitive methods with security annotations
- Flag: Public methods without @Secured or @PreAuthorize
- Fix: Add appropriate authorization annotation

**Rule: Authentication Bypass** | Severity: Critical
- Ensure all sensitive endpoints require authentication
- Flag: API endpoints without @Secured
- Fix: Add Spring Security configuration

---

### Cryptography

**Rule: Weak Hashing** | Severity: Critical
- Use strong hash algorithms (MD5, SHA-1 are weak)
- Flag: "MD5", "SHA-1" used for passwords
- Fix: Use bcrypt, scrypt, or PBKDF2

**Rule: Hardcoded Encryption Key** | Severity: Critical
- Never hardcode encryption keys
- Flag: Encryption key in source code
- Fix: Load from configuration

---

## 8. SonarQube-Compatible Issues (20+ rules)

**Rule: Code Duplication** | Severity: Medium
- Methods with identical logic should be merged
- Flag: Code duplication >3%
- Fix: Extract common code to helper method

**Rule: Dead Code** | Severity: Low
- Remove unused methods, variables, imports
- Flag: Unused imports, empty methods, never-called methods
- Fix: Delete or add @SuppressWarnings if intentional

**Rule: Type Safety** | Severity: High
- Avoid raw types, use generics everywhere
- Flag: `new ArrayList()` instead of `new ArrayList<String>()`
- Flag: Unchecked casts
- Fix: Add type parameters: `List<String> list = new ArrayList<>()`

---

## 9. Additional Issues (20+ rules)

### Error Handling

**Rule: Swallow Exceptions** | Severity: High
- Never ignore exceptions silently
- Flag: `catch (Exception e) { } // ignore`
- Fix: Log exception or rethrow

**Rule: Generic Exception Catching** | Severity: Medium
- Catch specific exceptions, not generic Exception
- Flag: `catch (Exception e)` instead of specific types
- Fix: Catch IOException, SQLException, etc. specifically

**Rule: Missing Finally/Try-With-Resources** | Severity: High
- Use try-with-resources for auto-closing resources
- Flag: Resources opened outside try-with-resources
- Fix: Use try-with-resources block

---

### Logging

**Rule: Missing Critical Logging** | Severity: Medium
- Log important business operations
- Flag: No logging for: method entry/exit, database operations, exceptions
- Fix: Add appropriate log statements

**Rule: Log Level Misuse** | Severity: Low
- Use correct log level: DEBUG < INFO < WARN < ERROR
- Flag: Business events logged as DEBUG
- Flag: Stack traces logged as INFO
- Fix: Use appropriate level

---

### Testing

**Rule: Missing Unit Tests** | Severity: High
- All public methods should have unit tests
- Flag: Coverage <70%
- Fix: Add unit tests

**Rule: Weak Test Assertions** | Severity: Medium
- Tests should assert expected values, not just "no exception"
- Flag: Tests with no assertions
- Fix: Add meaningful assertions

---

### Dependencies

**Rule: Dependency Vulnerability** | Severity: Critical
- No known vulnerabilities in dependencies
- Check: CVE database against dependency versions
- Fix: Update to patched version

**Rule: Conflicting Dependencies** | Severity: High
- Resolve all dependency conflicts
- Flag: Conflicting versions in dependency tree
- Fix: Use Maven exclusions or update parent version

---

## Severity Scale

- **Critical** (🔴): Security, data loss, crash risk → MUST FIX before merge
- **High** (🟠): Performance, architecture → Should fix before merge
- **Medium** (🟡): Code quality, maintainability → Fix in soon PR
- **Low** (🔵): Style, documentation → Nice to have
- **Info** (⚪): Suggestions, best practices → FYI

---

## Rule Application Examples

### Example 1: Cyclomatic Complexity
```java
// ❌ VIOLATION: 11+ decision points
public String calculateTax(User user, Order order) {
    if (user.isVIP()) {  // 1
        if (order.getTotal() > 1000) {  // 2
            ...
        } else if (order.getTotal() > 500) {  // 3
            ...
        } else {  // 4
            ...
        }
    } else if (user.isPremium()) {  // 5
        // More conditions...
    }
    // ...
    return result;
}

// ✅ FIXED: Extracted helper methods
public String calculateTax(User user, Order order) {
    if (user.isVIP()) {
        return calculateVIPTax(order);
    } else if (user.isPremium()) {
        return calculatePremiumTax(order);
    }
    return calculateStandardTax(order);
}
```

### Example 2: String Concatenation in Loop
```java
// ❌ VIOLATION: String concatenation in loop
String result = "";
for (String item : items) {
    result += item + ", ";
}

// ✅ FIXED: Use StringBuilder
StringBuilder sb = new StringBuilder();
for (String item : items) {
    sb.append(item).append(", ");
}
String result = sb.toString();
```

### Example 3: SQL Injection
```java
// ❌ VIOLATION: String concatenation in SQL
String sql = "SELECT * FROM users WHERE id = " + userId;
ResultSet rs = statement.executeQuery(sql);

// ✅ FIXED: Use prepared statement
String sql = "SELECT * FROM users WHERE id = ?";
PreparedStatement ps = connection.prepareStatement(sql);
ps.setInt(1, userId);
ResultSet rs = ps.executeQuery();
```

---

## Customization

You can customize which rules apply to your project:

```json
{
  "rules": {
    "PublicAPIJavaDoc": {
      "enabled": true,
      "severity": "Medium"
    },
    "CyclomaticComplexity": {
      "enabled": true,
      "severity": "High",
      "threshold": 10  // Custom threshold
    },
    "ClassSize": {
      "enabled": false  // Disabled for this project
    }
  }
}
```

See [CONFIGURATION.md](CONFIGURATION.md) for how to apply custom rule configurations.
