# Quick Start Guide

Get started with the Agent Skills Kit in 5 minutes!

## What You'll Learn

- How to install the Agent Skills Kit
- How AI agents discover and use skills
- How to create your first custom skill

## Step 1: Installation (1 minute)

### Option A: Copy to Your Project

```bash
# Clone this repository
git clone https://github.com/yeeehaooo/agent-kit.git

# Copy the skills directory to your project
cp -r agent-kit/.github/skills /path/to/your-project/.github/

# Commit the skills
cd /path/to/your-project
git add .github/skills
git commit -m "Add agent skills"
```

### Option B: Git Submodule (Advanced)

```bash
# Add as a submodule
cd your-project
git submodule add https://github.com/yeeehaooo/agent-kit.git .github/agent-kit

# Create a symlink to the skills directory
ln -s .github/agent-kit/.github/skills .github/skills
```

## Step 2: Verify Installation (30 seconds)

Check that the skills are in place:

```bash
ls -la .github/skills/
```

You should see:
```
api-client-patterns/
code-review-checklist/
deployment-workflow/
documentation-guide/
project-setup/
unit-testing/
```

## Step 3: Test with GitHub Copilot (2 minutes)

### In VS Code:

1. Open a file where you want to write a test
2. Start typing a comment: `// write a unit test for`
3. GitHub Copilot will suggest code following the patterns from the `unit-testing` skill

### Example:

```javascript
// Write a unit test for the calculateTotal function

// Copilot will suggest something like:
import { describe, it, expect } from '@jest/globals';

describe('calculateTotal', () => {
  it('should calculate total with tax', () => {
    // Arrange
    const price = 100;
    const taxRate = 0.08;
    
    // Act
    const total = calculateTotal(price, taxRate);
    
    // Assert
    expect(total).toBe(108);
  });
});
```

## Step 4: Create Your First Custom Skill (2 minutes)

Create a project-specific skill:

```bash
# Create a new skill directory
mkdir -p .github/skills/my-custom-skill

# Create the SKILL.md file
cat > .github/skills/my-custom-skill/SKILL.md << 'EOF'
---
name: my-custom-skill
description: Custom skill for my project's specific patterns
---

# My Custom Skill

## Overview

This skill teaches agents about our project-specific conventions.

## When to Use

- When working with our authentication system
- When implementing our data validation patterns

## Guidelines

### Authentication Pattern

Always use our AuthService:

```javascript
import { AuthService } from '@/services/auth';

async function protectedEndpoint(req, res) {
  const user = await AuthService.verifyToken(req.headers.authorization);
  if (!user) {
    return res.status(401).json({ error: 'Unauthorized' });
  }
  // Handle request
}
```

### Validation Pattern

Use Zod for all input validation:

```javascript
import { z } from 'zod';

const UserSchema = z.object({
  email: z.string().email(),
  name: z.string().min(2)
});

function validateUser(data) {
  return UserSchema.parse(data);
}
```
EOF
```

## Common Use Cases

### 1. Writing Tests

When you need to write tests, reference the `unit-testing` skill:

```javascript
// The agent will follow AAA pattern, include edge cases, and use proper naming
```

### 2. Creating Documentation

When documenting code, the `documentation-guide` skill helps:

```javascript
/**
 * The agent will create JSDoc comments following best practices
 */
```

### 3. Code Review

Use the `code-review-checklist` when reviewing PRs:

```markdown
The agent will check for:
- Security issues
- Performance problems
- Test coverage
- Code quality
```

### 4. Setting Up Projects

The `project-setup` skill guides new project initialization:

```bash
# The agent knows how to structure projects for different languages
```

### 5. API Integration

The `api-client-patterns` skill provides robust API client patterns:

```javascript
// The agent will include error handling, retries, and proper authentication
```

## Tips for Best Results

### 1. Be Specific in Comments

❌ Bad:
```javascript
// make an API call
```

✓ Good:
```javascript
// Create an authenticated API client following our patterns with retries
```

### 2. Reference Skills Explicitly

You can mention skills in comments:

```javascript
// Following the unit-testing skill, write tests for this function
```

### 3. Combine Skills

Skills work together:

```javascript
// Using project-setup and deployment-workflow skills, set up CI/CD
```

### 4. Update Skills

Keep skills current with your evolving practices:

```bash
# Edit existing skills
vim .github/skills/unit-testing/SKILL.md

# Commit changes
git add .github/skills
git commit -m "Update testing standards"
```

## Troubleshooting

### Skills Not Working?

1. **Check file location**: Skills must be in `.github/skills/` directory
2. **Verify YAML frontmatter**: Each SKILL.md must have valid frontmatter
3. **Restart your editor**: Some AI tools need a restart to detect new skills
4. **Check AI agent settings**: Ensure skills are enabled in your AI tool

### Skills Not Relevant?

1. **Customize them**: Edit skills to match your needs
2. **Create new ones**: Add project-specific skills
3. **Remove unused ones**: Delete skills that don't apply

## Next Steps

- Read the [SKILLS_INDEX.md](SKILLS_INDEX.md) for detailed skill descriptions
- Check [CONTRIBUTING.md](CONTRIBUTING.md) to add your own skills
- Explore each skill in `.github/skills/` directory
- Customize skills for your team's needs

## Learn More

- [Agent Skills Specification](https://agentskills.io/specification)
- [GitHub Copilot Documentation](https://docs.github.com/en/copilot)
- [VS Code Agent Skills](https://code.visualstudio.com/docs/copilot/customization/agent-skills)

## Need Help?

- Open an [issue](https://github.com/yeeehaooo/agent-kit/issues) for bugs
- Start a [discussion](https://github.com/yeeehaooo/agent-kit/discussions) for questions
- Check existing issues and discussions first

---

Happy coding with AI! 🚀
