---
name: documentation-guide
description: Standards and best practices for code documentation, API docs, and README files to maintain clear and comprehensive project documentation.
---

# Documentation Guide

## Overview

This skill provides comprehensive guidelines for creating and maintaining high-quality documentation across your codebase, including inline comments, API documentation, and README files.

## When to Use

- When creating new public APIs or modules
- When writing README files for projects
- When documenting complex logic or algorithms
- For every new feature or significant change
- When onboarding new team members

## General Principles

### Documentation Should Be:

1. **Clear**: Easy to understand for the target audience
2. **Concise**: No unnecessary words, but complete enough to be useful
3. **Current**: Updated alongside code changes
4. **Accurate**: Reflects the actual implementation
5. **Accessible**: Easy to find and navigate

## Code Documentation

### Inline Comments

Use comments to explain **why**, not **what** the code does:

```javascript
// ❌ Bad: Obvious comment
// Increment counter by 1
counter++;

// ✓ Good: Explains reasoning
// Skip cache for admin users to ensure they see real-time data
if (user.isAdmin) {
  bypassCache = true;
}
```

### When to Comment

**DO comment:**
- Complex algorithms or business logic
- Non-obvious decisions or trade-offs
- Workarounds for bugs or limitations
- Performance-critical sections
- Security-sensitive code

**DON'T comment:**
- Obvious code that's self-explanatory
- What properly named functions/variables already express
- Outdated information (remove instead)

### JSDoc (JavaScript/TypeScript)

```javascript
/**
 * Calculates the total price including tax and discounts.
 * 
 * @param {number} basePrice - The original price before adjustments
 * @param {number} taxRate - Tax rate as a decimal (e.g., 0.08 for 8%)
 * @param {number} [discount=0] - Optional discount amount
 * @returns {number} The final price after tax and discount
 * @throws {Error} If basePrice or taxRate is negative
 * 
 * @example
 * const total = calculateTotal(100, 0.08, 10);
 * // Returns: 98 (100 - 10 + 8% tax)
 */
function calculateTotal(basePrice, taxRate, discount = 0) {
  if (basePrice < 0 || taxRate < 0) {
    throw new Error('Price and tax rate must be non-negative');
  }
  return (basePrice - discount) * (1 + taxRate);
}
```

### Python Docstrings

```python
def process_data(data: dict, options: dict = None) -> dict:
    """
    Process raw data according to specified options.
    
    This function validates, transforms, and enriches the input data
    based on the provided configuration options.
    
    Args:
        data: Raw input data dictionary containing at least 'id' and 'value' keys
        options: Optional configuration dict with processing parameters
            - 'normalize' (bool): Whether to normalize values (default: False)
            - 'format' (str): Output format ('json' or 'xml', default: 'json')
    
    Returns:
        Processed data dictionary with additional metadata fields:
        - 'processed_at': Timestamp of processing
        - 'status': Processing status ('success' or 'error')
    
    Raises:
        ValueError: If required keys are missing from data
        KeyError: If invalid option keys are provided
    
    Example:
        >>> data = {'id': 123, 'value': 42}
        >>> result = process_data(data, {'normalize': True})
        >>> result['status']
        'success'
    
    Note:
        Large datasets (>10MB) should use process_data_stream() instead
        for better memory efficiency.
    """
    # Implementation here
    pass
```

### Java Javadoc

```java
/**
 * Validates user credentials against the authentication service.
 * 
 * <p>This method performs the following checks:
 * <ol>
 *   <li>Validates email format</li>
 *   <li>Checks password strength requirements</li>
 *   <li>Verifies against stored credentials</li>
 * </ol>
 * 
 * @param email the user's email address (must be non-null and valid format)
 * @param password the user's password (must meet complexity requirements)
 * @return {@code true} if credentials are valid, {@code false} otherwise
 * @throws IllegalArgumentException if email or password is null
 * @throws AuthenticationException if the service is unavailable
 * 
 * @see #resetPassword(String)
 * @since 1.2.0
 */
public boolean validateCredentials(String email, String password) 
    throws AuthenticationException {
    // Implementation
}
```

## API Documentation

### REST API Endpoints

Document each endpoint with:

```markdown
### POST /api/users

Creates a new user account.

**Authentication**: Required (Bearer token)

**Request Body**:
```json
{
  "name": "string (required, 2-50 characters)",
  "email": "string (required, valid email)",
  "role": "string (optional, one of: 'user', 'admin', default: 'user')"
}
```

**Response**: 201 Created
```json
{
  "id": "string (UUID)",
  "name": "string",
  "email": "string",
  "role": "string",
  "createdAt": "string (ISO 8601 timestamp)"
}
```

**Error Responses**:
- `400 Bad Request`: Invalid input data
- `401 Unauthorized`: Missing or invalid authentication token
- `409 Conflict`: Email already exists
- `500 Internal Server Error`: Server error

**Example**:
```bash
curl -X POST https://api.example.com/api/users \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"name": "John Doe", "email": "john@example.com"}'
```
```

### GraphQL Schema

```graphql
"""
Represents a user in the system.
"""
type User {
  """
  Unique identifier for the user.
  """
  id: ID!
  
  """
  User's full name (2-50 characters).
  """
  name: String!
  
  """
  User's email address (must be valid and unique).
  """
  email: String!
  
  """
  User's role in the system.
  """
  role: UserRole!
  
  """
  Timestamp when the user was created.
  """
  createdAt: DateTime!
}

"""
Available user roles.
"""
enum UserRole {
  """
  Regular user with standard permissions.
  """
  USER
  
  """
  Administrator with elevated permissions.
  """
  ADMIN
}
```

## README Files

### Project README Structure

```markdown
# Project Name

Brief one-sentence description of what this project does.

## Features

- List key features
- Highlight what makes it unique
- Use bullet points for clarity

## Installation

### Prerequisites

- Node.js 18+ (or relevant requirements)
- PostgreSQL 14+
- Any other dependencies

### Setup

```bash
# Clone the repository
git clone https://github.com/username/project.git

# Install dependencies
npm install

# Configure environment
cp .env.example .env
# Edit .env with your settings

# Run database migrations
npm run migrate

# Start the development server
npm run dev
```

## Usage

### Basic Example

```javascript
import { SomeClass } from 'project';

const instance = new SomeClass({
  apiKey: 'your-api-key'
});

const result = await instance.doSomething();
console.log(result);
```

### Advanced Usage

Document more complex scenarios here.

## Configuration

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `apiKey` | string | - | Your API key (required) |
| `timeout` | number | 5000 | Request timeout in ms |
| `retries` | number | 3 | Number of retry attempts |

## API Reference

Link to full API documentation or provide inline documentation for main APIs.

## Development

### Running Tests

```bash
npm test
```

### Building

```bash
npm run build
```

### Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

## Troubleshooting

### Common Issues

**Problem**: Error message X
**Solution**: Do Y to fix it

## License

MIT License - see [LICENSE](LICENSE) for details

## Support

- Documentation: https://docs.example.com
- Issues: https://github.com/username/project/issues
- Discussions: https://github.com/username/project/discussions
```

## Module/Package Documentation

For individual modules or packages:

```markdown
# Module Name

## Purpose

Brief description of what this module does and why it exists.

## Exports

### `functionName(param1, param2)`

Description of what the function does.

**Parameters:**
- `param1` (Type): Description
- `param2` (Type): Description

**Returns:** Type - Description

**Example:**
```javascript
const result = functionName('value1', 'value2');
```

### `ClassName`

Description of the class.

**Constructor:**
```javascript
new ClassName(options)
```

**Methods:**
- `method1()`: Description
- `method2()`: Description
```

## Architecture Documentation

### High-Level Architecture

```markdown
# System Architecture

## Overview

Brief description of the system architecture.

## Components

### Frontend
- Technology stack
- Key libraries
- Structure

### Backend
- API layer
- Business logic
- Data layer

### Infrastructure
- Deployment
- Databases
- External services

## Data Flow

1. User initiates action
2. Request flows through middleware
3. Business logic processes
4. Data persists to database
5. Response returns to user

## Diagrams

[Include architecture diagrams, flowcharts, etc.]
```

## Best Practices

### 1. Keep Documentation Close to Code

- Put docs in the same directory as the code
- Use inline documentation for complex functions
- Maintain a docs folder for broader documentation

### 2. Use Examples

Always include practical examples:

```javascript
// ✓ Good: Real-world example
/**
 * Formats a date for display
 * @example
 * formatDate(new Date('2024-01-15'))
 * // Returns: "January 15, 2024"
 */
```

### 3. Document Edge Cases

```javascript
/**
 * Merges two arrays, removing duplicates.
 * 
 * @param {Array} arr1 - First array
 * @param {Array} arr2 - Second array
 * @returns {Array} Merged array without duplicates
 * 
 * @example
 * merge([1, 2], [2, 3]) // [1, 2, 3]
 * merge([], [1, 2]) // [1, 2]
 * merge([1], []) // [1]
 * merge([], []) // []
 */
```

### 4. Version Documentation

- Include version numbers for breaking changes
- Use semantic versioning
- Maintain a CHANGELOG.md

### 5. Regular Updates

- Review and update docs with each release
- Remove outdated information
- Add deprecation notices when needed

## Documentation Tools

### Auto-generation Tools

- **JSDoc**: JavaScript/TypeScript
- **Sphinx**: Python
- **Javadoc**: Java
- **Doxygen**: C/C++
- **RDoc**: Ruby
- **GoDoc**: Go

### Documentation Hosting

- GitHub Pages
- Read the Docs
- GitBook
- Docusaurus
- VuePress

## Accessibility

Ensure documentation is accessible:

- Use clear headings (H1, H2, H3)
- Provide alt text for images
- Use descriptive link text
- Ensure code examples are properly formatted
- Consider different screen readers

## Internationalization

For projects with international users:

- Keep language simple and clear
- Avoid idioms and colloquialisms
- Consider providing translations
- Use inclusive examples

## Commands

```bash
# Generate API docs
npm run docs:generate

# Serve docs locally
npm run docs:serve

# Lint documentation
npm run docs:lint

# Build documentation site
npm run docs:build
```

## Quality Checklist

Before publishing documentation:

- [ ] All code examples are tested and working
- [ ] Links are valid and not broken
- [ ] Grammar and spelling are correct
- [ ] Screenshots are up-to-date
- [ ] API signatures match current code
- [ ] Installation instructions are accurate
- [ ] Examples cover common use cases
- [ ] Edge cases are documented
- [ ] Version information is current

## Anti-Patterns to Avoid

❌ **Redundant comments:**
```javascript
// Set name to John
name = "John";
```

❌ **Outdated documentation:**
```javascript
// Returns array (NOTE: This is wrong now)
function getUsers() {
  return new Map(); // Changed to Map but docs not updated
}
```

❌ **Vague descriptions:**
```javascript
/**
 * Does stuff with data
 */
```

✓ **Clear and specific:**
```javascript
/**
 * Transforms raw CSV data into structured JSON objects.
 * Handles missing fields by setting them to null.
 */
```

## Resources

- [Write the Docs](https://www.writethedocs.org/)
- [Google Developer Documentation Style Guide](https://developers.google.com/style)
- [Microsoft Writing Style Guide](https://learn.microsoft.com/en-us/style-guide/)
- [JSDoc Reference](https://jsdoc.app/)
