# Agent Skills Index

This document provides a comprehensive overview of all available skills in the Agent Skills Kit.

## Quick Reference

| Skill Name | Category | Languages | Difficulty |
|------------|----------|-----------|------------|
| [unit-testing](#unit-testing) | Testing | JS/TS, Python, Java | Beginner |
| [documentation-guide](#documentation-guide) | Documentation | All | Beginner |
| [code-review-checklist](#code-review-checklist) | Quality | All | Intermediate |
| [deployment-workflow](#deployment-workflow) | DevOps | All | Advanced |
| [project-setup](#project-setup) | Setup | JS/TS, Python, Java | Intermediate |
| [api-client-patterns](#api-client-patterns) | Integration | JS/TS, Python | Intermediate |

## Detailed Skill Descriptions

### unit-testing

**Location**: `.github/skills/unit-testing/SKILL.md`

**Purpose**: Provides comprehensive patterns and best practices for writing effective unit tests.

**Key Topics**:
- Test structure (Arrange-Act-Assert pattern)
- Naming conventions
- Language-specific frameworks (Jest, pytest, JUnit)
- Test isolation and mocking
- Edge case handling
- Coverage guidelines

**Best For**:
- Writing new test suites
- Improving existing tests
- Establishing testing standards
- Code review of test code

---

### documentation-guide

**Location**: `.github/skills/documentation-guide/SKILL.md`

**Purpose**: Standards and best practices for maintaining clear and comprehensive documentation.

**Key Topics**:
- Inline code comments
- API documentation (JSDoc, Docstrings, Javadoc)
- README structure
- Architecture documentation
- Code examples and edge cases
- Documentation tools

**Best For**:
- Creating project documentation
- Documenting APIs
- Writing README files
- Maintaining technical documentation

---

### code-review-checklist

**Location**: `.github/skills/code-review-checklist/SKILL.md`

**Purpose**: Comprehensive guidelines for conducting thorough and constructive code reviews.

**Key Topics**:
- Review philosophy and mindset
- Functionality verification
- Code quality assessment
- Security review
- Performance considerations
- Testing coverage
- Providing constructive feedback

**Best For**:
- Reviewing pull requests
- Establishing review standards
- Training new reviewers
- Improving code quality

---

### deployment-workflow

**Location**: `.github/skills/deployment-workflow/SKILL.md`

**Purpose**: Systematic procedures for safe, reliable, and repeatable deployments.

**Key Topics**:
- Pre-deployment checklist
- Deployment strategies (rolling, blue-green, canary)
- Database migrations
- Rollback procedures
- Monitoring and verification
- CI/CD integration

**Best For**:
- Production deployments
- Setting up deployment pipelines
- Incident response
- Release planning

---

### project-setup

**Location**: `.github/skills/project-setup/SKILL.md`

**Purpose**: Templates and guidelines for initializing new projects with proper structure.

**Key Topics**:
- Project structure templates
- Language-specific setups (Node.js, Python, Java, React)
- Configuration files (.gitignore, .env, tsconfig, etc.)
- CI/CD setup
- Docker configuration
- Development tooling

**Best For**:
- Starting new projects
- Standardizing project structure
- Setting up microservices
- Creating libraries or packages

---

### api-client-patterns

**Location**: `.github/skills/api-client-patterns/SKILL.md`

**Purpose**: Best practices for building robust, maintainable API clients.

**Key Topics**:
- Client architecture
- Error handling and retries
- Authentication patterns (JWT, API keys)
- Pagination strategies
- Caching and rate limiting
- Request batching
- Testing API clients

**Best For**:
- Integrating third-party APIs
- Building internal API clients
- Creating SDK libraries
- Microservice communication

---

## Using Skills

### For GitHub Copilot

Skills are automatically discovered when placed in the `.github/skills/` directory. GitHub Copilot will reference relevant skills based on your current coding context.

### For Other AI Agents

Most modern AI coding assistants support the SKILL.md format. Ensure your agent is configured to read from the `.github/skills/` directory.

### For Teams

1. **Copy skills to your project**: Place the `.github/skills/` directory in your repository
2. **Customize as needed**: Modify skills to match your team's conventions
3. **Add project-specific skills**: Create new skills for domain-specific patterns
4. **Keep skills updated**: Update skills as your practices evolve

## Skill Dependencies

Some skills reference or build upon others:

- **code-review-checklist** references **unit-testing** for test review
- **deployment-workflow** references **project-setup** for CI/CD configuration
- **project-setup** references **documentation-guide** for README templates

## Contributing New Skills

See [CONTRIBUTING.md](../CONTRIBUTING.md) for guidelines on:
- Creating new skills
- Improving existing skills
- Skill format requirements
- Submission process

## Skill Versioning

Skills follow semantic versioning:
- **Major**: Breaking changes to skill structure or major content overhauls
- **Minor**: Adding new sections or significant enhancements
- **Patch**: Bug fixes, typos, minor clarifications

Current version: **1.0.0**

## Feedback and Improvements

We welcome feedback on all skills:
- Open an issue for bugs or unclear content
- Submit a PR for improvements
- Share your experience using the skills
- Request new skills or topics

## Resources

- [Agent Skills Specification](https://agentskills.io/specification)
- [GitHub Copilot Documentation](https://docs.github.com/en/copilot)
- [VS Code Agent Skills](https://code.visualstudio.com/docs/copilot/customization/agent-skills)

---

Last updated: 2024-01-08
