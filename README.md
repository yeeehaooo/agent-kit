# Agent Skills Kit

A comprehensive collection of reusable agent skills for AI coding assistants like GitHub Copilot, Claude, and other AI agents. This kit provides structured, modular capabilities that teach AI agents your repository's patterns, conventions, and workflows.

## What are Agent Skills?

Agent Skills are modular, standardized capabilities that equip AI agents with specialized knowledge and workflows. Each skill is a self-contained package of instructions, examples, and best practices stored in the `.github/skills/` directory.

## Features

- 🎯 **Modular Design**: Each skill tackles a specific task
- 📚 **Standardized Format**: Compatible with GitHub Copilot, Claude, and other AI agents
- 🔄 **Reusable**: Share and distribute skills across projects and teams
- 🚀 **Easy to Use**: Simply place skills in `.github/skills/` directory
- 📖 **Well Documented**: Each skill includes detailed instructions and examples

## Available Skills

This kit includes the following agent skills:

- **unit-testing**: Patterns and best practices for writing unit tests
- **documentation-guide**: Standards for code documentation and README files
- **code-review-checklist**: Guidelines for thorough code reviews
- **deployment-workflow**: Procedures for safe and reliable deployments
- **project-setup**: Templates for initializing new projects
- **api-client-patterns**: Best practices for API integration

## Installation

To use these skills in your project:

1. Copy the `.github/skills/` directory to your repository
2. AI agents will automatically discover and use these skills when relevant
3. Customize skills to match your team's conventions

## Usage

### For GitHub Copilot

Skills are automatically loaded when you use GitHub Copilot in VS Code or your IDE. The agent will reference relevant skills based on your current task.

### For Other AI Agents

Most modern AI coding assistants support the SKILL.md format. Simply ensure your agent is configured to read from the `.github/skills/` directory.

## Skill Format

Each skill follows this structure:

```
.github/skills/
    └── skill-name/
        ├── SKILL.md      # Required: metadata and instructions
        └── examples/     # Optional: code examples or templates
```

The `SKILL.md` file starts with YAML frontmatter:

```markdown
---
name: skill-name
description: Brief description of what the skill does
---

# Detailed instructions follow...
```

## Contributing

We welcome contributions! To add a new skill:

1. Create a new directory under `.github/skills/`
2. Add a `SKILL.md` file with proper frontmatter
3. Include detailed instructions, examples, and edge cases
4. Submit a pull request

## Customization

Feel free to modify these skills to match your team's specific needs:

- Update coding standards
- Add language-specific conventions
- Include project-specific workflows
- Adjust to your deployment procedures

## License

MIT License - See LICENSE file for details

## Resources

- [Agent Skills Specification](https://agentskills.io/specification)
- [GitHub Copilot Documentation](https://docs.github.com/en/copilot)
- [VS Code Agent Skills Guide](https://code.visualstudio.com/docs/copilot/customization/agent-skills)

## Support

For issues or questions, please open an issue in this repository.