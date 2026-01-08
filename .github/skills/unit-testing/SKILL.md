---
name: unit-testing
description: Comprehensive patterns and best practices for writing effective unit tests across multiple languages and frameworks.
---

# Unit Testing Best Practices

## Overview

This skill provides guidelines for writing maintainable, reliable, and comprehensive unit tests. Follow these patterns to ensure consistent test quality across your codebase.

## When to Use

- When creating new functions or methods
- When fixing bugs (write a failing test first)
- When refactoring existing code
- For every pull request before merging

## General Principles

### Test Structure

Follow the **Arrange-Act-Assert (AAA)** pattern:

```javascript
// Arrange: Set up test data and preconditions
const input = { userId: 123, name: 'John' };
const expected = 'User: John (123)';

// Act: Execute the function being tested
const result = formatUser(input);

// Assert: Verify the outcome
expect(result).toBe(expected);
```

### Naming Conventions

Use descriptive test names that explain what is being tested and the expected outcome:

**Good:**
```javascript
test('formatUser returns formatted string with name and ID when given valid user object')
test('calculateTotal throws error when price is negative')
test('isValidEmail returns false for malformed email addresses')
```

**Bad:**
```javascript
test('test1')
test('formatUser works')
test('handles edge case')
```

## Language-Specific Guidelines

### JavaScript/TypeScript (Jest/Vitest)

```javascript
import { describe, it, expect, beforeEach } from '@jest/globals';

describe('UserService', () => {
  let userService;

  beforeEach(() => {
    userService = new UserService();
  });

  it('should create user with valid data', async () => {
    const userData = { name: 'Alice', email: 'alice@example.com' };
    
    const user = await userService.createUser(userData);
    
    expect(user.id).toBeDefined();
    expect(user.name).toBe('Alice');
    expect(user.email).toBe('alice@example.com');
  });

  it('should throw error when email is invalid', async () => {
    const userData = { name: 'Bob', email: 'invalid-email' };
    
    await expect(userService.createUser(userData))
      .rejects.toThrow('Invalid email address');
  });
});
```

### Python (pytest)

```python
import pytest
from my_module import Calculator

class TestCalculator:
    @pytest.fixture
    def calculator(self):
        return Calculator()

    def test_add_positive_numbers(self, calculator):
        # Arrange
        a, b = 5, 3
        
        # Act
        result = calculator.add(a, b)
        
        # Assert
        assert result == 8

    def test_divide_by_zero_raises_error(self, calculator):
        with pytest.raises(ZeroDivisionError):
            calculator.divide(10, 0)
```

### Java (JUnit)

```java
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.BeforeEach;
import static org.junit.jupiter.api.Assertions.*;

class UserValidatorTest {
    private UserValidator validator;

    @BeforeEach
    void setUp() {
        validator = new UserValidator();
    }

    @Test
    void shouldReturnTrueForValidUser() {
        User user = new User("John", "john@example.com");
        
        boolean isValid = validator.validate(user);
        
        assertTrue(isValid);
    }

    @Test
    void shouldThrowExceptionForNullUser() {
        assertThrows(IllegalArgumentException.class, () -> {
            validator.validate(null);
        });
    }
}
```

## Best Practices

### 1. Test Isolation

Each test should be independent and not rely on others:

```javascript
// ✓ Good: Each test is self-contained
test('should add item to empty cart', () => {
  const cart = new Cart();
  cart.addItem('apple');
  expect(cart.items.length).toBe(1);
});

test('should remove item from cart', () => {
  const cart = new Cart();
  cart.addItem('apple');
  cart.removeItem('apple');
  expect(cart.items.length).toBe(0);
});
```

### 2. Mock External Dependencies

Use mocks for external services, databases, and APIs:

```javascript
import { vi } from 'vitest';

test('should fetch user from API', async () => {
  // Mock the fetch function
  const mockFetch = vi.fn().mockResolvedValue({
    json: async () => ({ id: 1, name: 'Alice' })
  });
  global.fetch = mockFetch;

  const user = await fetchUser(1);

  expect(mockFetch).toHaveBeenCalledWith('/api/users/1');
  expect(user.name).toBe('Alice');
});
```

### 3. Test Edge Cases

Always consider and test edge cases:

```javascript
describe('parseInteger', () => {
  it('should parse valid integer strings', () => {
    expect(parseInteger('42')).toBe(42);
  });

  it('should handle negative numbers', () => {
    expect(parseInteger('-10')).toBe(-10);
  });

  it('should handle zero', () => {
    expect(parseInteger('0')).toBe(0);
  });

  it('should throw error for invalid input', () => {
    expect(() => parseInteger('abc')).toThrow();
  });

  it('should throw error for null or undefined', () => {
    expect(() => parseInteger(null)).toThrow();
    expect(() => parseInteger(undefined)).toThrow();
  });
});
```

### 4. Keep Tests Fast

- Avoid unnecessary delays or timeouts
- Mock slow operations (database, network calls)
- Use in-memory databases for integration tests

### 5. Maintain Test Code Quality

- Follow the same coding standards as production code
- Refactor common setup into helper functions
- Keep tests readable and maintainable

## Common Patterns

### Testing Async Code

```javascript
// Using async/await
test('should resolve with data', async () => {
  const data = await fetchData();
  expect(data).toBeDefined();
});

// Testing promises
test('should reject with error', () => {
  return expect(fetchInvalidData()).rejects.toThrow('Not found');
});
```

### Testing Callbacks

```javascript
test('should call callback with result', (done) => {
  processData((result) => {
    expect(result).toBe('processed');
    done();
  });
});
```

### Parameterized Tests

```javascript
// Jest
test.each([
  [1, 2, 3],
  [10, 20, 30],
  [-1, 1, 0],
])('add(%i, %i) should return %i', (a, b, expected) => {
  expect(add(a, b)).toBe(expected);
});
```

## Coverage Guidelines

- Aim for **80%+ code coverage** for critical paths
- Don't sacrifice test quality for coverage numbers
- Focus on testing behavior, not implementation details
- Cover happy paths and error scenarios

## Commands

```bash
# JavaScript/TypeScript
npm test                    # Run all tests
npm test -- --watch        # Watch mode
npm test -- --coverage     # Generate coverage report

# Python
pytest                     # Run all tests
pytest -v                  # Verbose output
pytest --cov=src          # Coverage report

# Java
mvn test                   # Maven
gradle test                # Gradle
```

## What NOT to Test

- Third-party libraries (trust they're tested)
- Framework internals
- Simple getters/setters (unless they have logic)
- Private methods directly (test through public interface)

## Edge Cases to Consider

- Null or undefined inputs
- Empty collections (arrays, objects, strings)
- Boundary values (min/max integers, dates)
- Invalid data types
- Concurrent access issues
- Network failures and timeouts

## Anti-Patterns to Avoid

❌ **Testing implementation details:**
```javascript
// Bad: Testing internal state
test('should set internal flag', () => {
  obj.process();
  expect(obj._internalFlag).toBe(true);
});
```

✓ **Test behavior instead:**
```javascript
// Good: Testing observable behavior
test('should mark process as complete', () => {
  obj.process();
  expect(obj.isComplete()).toBe(true);
});
```

❌ **Overly complex test setup:**
```javascript
// Bad: Too much setup obscures intent
test('complex test', () => {
  // 50 lines of setup
  // What are we actually testing?
});
```

✓ **Use helper functions:**
```javascript
// Good: Clear and concise
test('should validate user', () => {
  const user = createTestUser();
  expect(validator.validate(user)).toBe(true);
});
```

## Integration with CI/CD

Ensure tests run automatically:

- On every commit
- Before merging pull requests
- In CI pipeline (GitHub Actions, Jenkins, etc.)
- Block merges if tests fail

## Resources

- [Jest Documentation](https://jestjs.io/)
- [pytest Documentation](https://docs.pytest.org/)
- [JUnit 5 Documentation](https://junit.org/junit5/)
- [Testing Best Practices](https://testingjavascript.com/)
