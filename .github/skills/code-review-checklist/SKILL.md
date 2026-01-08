---
name: code-review-checklist
description: Comprehensive guidelines and checklist for conducting thorough and constructive code reviews focused on quality, security, and maintainability.
---

# Code Review Checklist

## Overview

This skill provides a systematic approach to reviewing code changes, ensuring quality, security, and maintainability. Use this checklist to conduct thorough and constructive code reviews.

## When to Use

- For every pull request before merging
- When reviewing colleague's code changes
- Before deploying to production
- During pair programming sessions

## Review Philosophy

### Goals of Code Review

1. **Find bugs and issues** before they reach production
2. **Share knowledge** across the team
3. **Maintain code quality** and consistency
4. **Improve design** through discussion
5. **Ensure security** and performance standards

### Reviewer Mindset

- Be **constructive**, not critical
- Ask **questions** rather than making demands
- **Praise** good solutions and improvements
- Focus on the **code**, not the person
- **Learn** from the changes being reviewed

## Pre-Review Checklist

Before diving into the code:

- [ ] Pull request has a clear title and description
- [ ] Relevant issue/ticket is linked
- [ ] CI/CD pipeline passes (tests, linting, builds)
- [ ] Branch is up-to-date with the target branch
- [ ] No merge conflicts exist
- [ ] Changes are reasonably sized (not too large)

## Core Review Areas

### 1. Functionality

**Does the code do what it's supposed to do?**

- [ ] Implements the described feature/fix correctly
- [ ] Edge cases are handled
- [ ] Error scenarios are covered
- [ ] Input validation is present
- [ ] Output is correct and expected

**Questions to ask:**
- What happens with null/undefined inputs?
- What happens with empty collections?
- What happens at boundary values (min/max)?
- Are there any race conditions?

```javascript
// ❌ Missing validation
function processUser(user) {
  return user.name.toUpperCase(); // Crashes if user or name is null
}

// ✓ Proper validation
function processUser(user) {
  if (!user || !user.name) {
    throw new Error('Invalid user object');
  }
  return user.name.toUpperCase();
}
```

### 2. Code Quality

**Is the code clean and maintainable?**

- [ ] Code is readable and self-documenting
- [ ] Functions/methods are focused and single-purpose
- [ ] Variable and function names are descriptive
- [ ] No duplicate code (DRY principle)
- [ ] No overly complex logic
- [ ] Proper abstraction levels
- [ ] Magic numbers are replaced with named constants

```javascript
// ❌ Unclear and complex
function p(x) {
  return x > 0 && x < 100 && x % 2 === 0;
}

// ✓ Clear and descriptive
function isValidEvenPercentage(percentage) {
  const MIN_PERCENTAGE = 0;
  const MAX_PERCENTAGE = 100;
  const isWithinRange = percentage > MIN_PERCENTAGE && percentage < MAX_PERCENTAGE;
  const isEven = percentage % 2 === 0;
  return isWithinRange && isEven;
}
```

### 3. Testing

**Is the code properly tested?**

- [ ] New functionality has corresponding tests
- [ ] Tests are clear and meaningful
- [ ] Edge cases are tested
- [ ] Tests follow AAA pattern (Arrange, Act, Assert)
- [ ] Test names are descriptive
- [ ] Mock external dependencies appropriately
- [ ] No flaky tests introduced
- [ ] Code coverage is maintained or improved

**Test quality check:**
```javascript
// ❌ Vague test
test('it works', () => {
  expect(func()).toBeTruthy();
});

// ✓ Descriptive test
test('calculateDiscount returns 10% off for premium members', () => {
  const member = { type: 'premium', total: 100 };
  expect(calculateDiscount(member)).toBe(10);
});
```

### 4. Security

**Is the code secure?**

- [ ] No hardcoded secrets or credentials
- [ ] Input is sanitized and validated
- [ ] SQL injection prevention (parameterized queries)
- [ ] XSS prevention (proper escaping)
- [ ] CSRF protection is in place
- [ ] Authentication and authorization are correct
- [ ] Sensitive data is encrypted
- [ ] No exposed internal error details to users
- [ ] Dependencies are up-to-date and vulnerability-free

**Common security issues:**

```javascript
// ❌ SQL Injection vulnerability
const query = `SELECT * FROM users WHERE id = ${userId}`;

// ✓ Parameterized query
const query = 'SELECT * FROM users WHERE id = ?';
db.execute(query, [userId]);

// ❌ XSS vulnerability
element.innerHTML = userInput;

// ✓ Safe text insertion
element.textContent = userInput;
// or use proper sanitization library
element.innerHTML = sanitizeHtml(userInput);
```

### 5. Performance

**Is the code efficient?**

- [ ] No unnecessary database queries
- [ ] Efficient algorithms and data structures
- [ ] Proper indexing for database queries
- [ ] Caching used where appropriate
- [ ] No memory leaks (cleanup in useEffect, event listeners)
- [ ] Large lists use pagination or virtualization
- [ ] Assets are optimized (images, bundles)
- [ ] No blocking operations on main thread

```javascript
// ❌ N+1 query problem
for (const user of users) {
  const posts = await db.query('SELECT * FROM posts WHERE userId = ?', user.id);
}

// ✓ Batch query
const userIds = users.map(u => u.id);
const posts = await db.query('SELECT * FROM posts WHERE userId IN (?)', userIds);
```

### 6. Design & Architecture

**Does the code follow good design principles?**

- [ ] Follows existing architectural patterns
- [ ] Adheres to SOLID principles
- [ ] Proper separation of concerns
- [ ] Appropriate abstraction level
- [ ] Dependencies are injected, not hardcoded
- [ ] No circular dependencies
- [ ] Interfaces/contracts are well-defined

```javascript
// ❌ Tight coupling
class UserService {
  constructor() {
    this.db = new Database('prod'); // Hardcoded dependency
  }
}

// ✓ Dependency injection
class UserService {
  constructor(database) {
    this.db = database; // Injected, testable
  }
}
```

### 7. Error Handling

**Are errors handled properly?**

- [ ] Try-catch blocks where needed
- [ ] Errors are logged appropriately
- [ ] User-friendly error messages
- [ ] No swallowed exceptions
- [ ] Fallback behavior for failures
- [ ] Async errors are caught

```javascript
// ❌ Silent failure
try {
  await processData();
} catch (error) {
  // Nothing here
}

// ✓ Proper error handling
try {
  await processData();
} catch (error) {
  logger.error('Failed to process data', { error, userId });
  throw new ApplicationError('Unable to process data', 500);
}
```

### 8. Documentation

**Is the code properly documented?**

- [ ] Complex logic has explanatory comments
- [ ] Public APIs have JSDoc/docstrings
- [ ] README updated if needed
- [ ] Architecture decisions documented
- [ ] Non-obvious decisions explained
- [ ] TODOs have tickets referenced

```javascript
// ✓ Good documentation
/**
 * Calculates shipping cost based on weight and destination.
 * Uses zone-based pricing model introduced in SHIP-123.
 * 
 * @param {number} weight - Package weight in kg
 * @param {string} zone - Destination zone code
 * @returns {number} Shipping cost in USD
 */
function calculateShipping(weight, zone) {
  // Complex calculation with business logic
  // Using 2024 rate card (see docs/shipping-rates.md)
}
```

### 9. Style & Conventions

**Does the code follow project conventions?**

- [ ] Follows established coding style
- [ ] Naming conventions are consistent
- [ ] File structure matches project organization
- [ ] Import/export patterns are consistent
- [ ] Linter rules are satisfied
- [ ] Formatter has been run
- [ ] No commented-out code (remove it)
- [ ] No console.logs in production code

### 10. Backwards Compatibility

**Are existing integrations safe?**

- [ ] API changes are backwards compatible or properly versioned
- [ ] Database migrations are reversible
- [ ] No breaking changes to public interfaces
- [ ] Deprecated features have migration path
- [ ] Feature flags used for risky changes

## Review Process

### Step-by-Step Review

1. **Understand the context**
   - Read the PR description
   - Understand the problem being solved
   - Check linked issues/tickets

2. **Review at different levels**
   - High-level: Architecture and design
   - Mid-level: Logic and algorithms
   - Low-level: Code quality and style

3. **Test locally** (for complex changes)
   - Pull the branch
   - Run tests
   - Try to break it
   - Check edge cases

4. **Provide feedback**
   - Be specific and actionable
   - Reference line numbers
   - Suggest improvements
   - Explain reasoning

### Feedback Categories

Use labels or prefixes to categorize feedback:

- **🔴 Blocker**: Must be fixed before merge
- **🟡 Concern**: Should be addressed or discussed
- **💡 Suggestion**: Nice-to-have improvement
- **❓ Question**: Need clarification
- **👍 Praise**: Good solution or improvement

### Example Feedback

**Good feedback:**
```
🔴 Blocker: Line 42
This function doesn't handle the case when `user.email` is null,
which will cause a crash. Consider adding a null check:

if (!user?.email) {
  throw new Error('Email is required');
}
```

**Bad feedback:**
```
This is wrong. Fix it.
```

## Common Issues to Watch For

### Anti-Patterns

1. **God Objects**: Classes that do too much
2. **Spaghetti Code**: Tangled, hard-to-follow logic
3. **Copy-Paste Programming**: Duplicated code blocks
4. **Magic Numbers**: Unexplained numeric constants
5. **Shotgun Surgery**: Changes scattered across many files
6. **Leaky Abstractions**: Implementation details leaking through interfaces

### Code Smells

- Functions longer than 50 lines
- More than 3-4 function parameters
- Deeply nested conditionals (>3 levels)
- Long parameter lists
- Large classes (>500 lines)
- Dead code or commented-out code

## Size Guidelines

### Pull Request Size

- **Small** (1-50 lines): Quick review, easy to understand
- **Medium** (50-200 lines): Standard review
- **Large** (200-500 lines): Needs focused time
- **Extra Large** (500+ lines): Consider splitting

**Recommendation**: Keep PRs under 400 lines for efficient review

## Checklist Template

Use this template for your reviews:

```markdown
## Review Checklist

### Functionality
- [ ] Feature/fix works as described
- [ ] Edge cases handled
- [ ] Error handling present

### Code Quality
- [ ] Code is readable and maintainable
- [ ] No code duplication
- [ ] Naming is clear and consistent

### Testing
- [ ] Tests added/updated
- [ ] Tests pass locally
- [ ] Coverage is adequate

### Security
- [ ] No security vulnerabilities
- [ ] Input validation present
- [ ] No hardcoded secrets

### Performance
- [ ] No obvious performance issues
- [ ] Efficient algorithms used

### Documentation
- [ ] Code comments where needed
- [ ] API docs updated
- [ ] README updated if necessary

### Additional Notes
[Any specific feedback or questions]
```

## Responding to Reviews

### As the Author

- **Don't take it personally**: Feedback is about code, not you
- **Ask for clarification**: If feedback is unclear
- **Discuss disagreements**: Explain your reasoning
- **Learn from feedback**: Improve for next time
- **Thank reviewers**: Appreciate their time and effort

### Handling Disagreements

1. **Discuss**: Have a conversation about different approaches
2. **Document**: Write down pros/cons of each approach
3. **Escalate**: Involve a senior engineer or architect if needed
4. **Compromise**: Find a middle ground when possible
5. **Move forward**: Make a decision and document the reasoning

## Automated Checks

Leverage automation to catch common issues:

- **Linters**: ESLint, Pylint, RuboCop
- **Formatters**: Prettier, Black, gofmt
- **Static Analysis**: SonarQube, CodeClimate
- **Security Scanners**: Snyk, npm audit, OWASP Dependency Check
- **Coverage**: Istanbul, Coverage.py
- **Type Checkers**: TypeScript, mypy, Flow

## Time Management

- Small PRs: 5-15 minutes
- Medium PRs: 15-30 minutes
- Large PRs: 30-60 minutes
- Take breaks for very large reviews
- Don't rush - quality over speed

## Resources

- [Google Engineering Practices](https://google.github.io/eng-practices/review/)
- [Conventional Comments](https://conventionalcomments.org/)
- [Code Review Best Practices](https://stackoverflow.blog/2019/09/30/how-to-make-good-code-reviews-better/)
- [The Art of Code Review](https://www.alexandra-hill.com/2018/06/25/the-art-of-giving-and-receiving-code-reviews/)

## Final Approval

Before approving:

- [ ] All blocking issues are resolved
- [ ] Tests pass
- [ ] Documentation is complete
- [ ] You would be comfortable maintaining this code
- [ ] Code meets team standards
