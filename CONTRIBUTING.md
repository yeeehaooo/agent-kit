# Contributing to Agent Skills Kit

Thank you for your interest in contributing to the Agent Skills Kit! This document provides guidelines for contributing new skills or improving existing ones.

## How to Contribute

### Reporting Issues

- Use the GitHub issue tracker to report bugs or suggest features
- Check if the issue already exists before creating a new one
- Provide detailed information about the problem or suggestion
- Include examples when applicable

### Adding New Skills

1. **Fork the repository** and create a new branch:
   ```bash
   git checkout -b feature/my-new-skill
   ```

2. **Create the skill directory**:
   ```bash
   mkdir -p .github/skills/my-skill-name
   ```

3. **Create SKILL.md** with the following structure:
   ```markdown
   ---
   name: my-skill-name
   description: Brief description of what the skill does
   ---

   # Skill Title

   ## Overview
   Detailed explanation of the skill

   ## When to Use
   When to apply this skill

   ## Guidelines
   Step-by-step instructions and examples
   ```

4. **Follow the skill format guidelines**:
   - Start with YAML frontmatter (name and description)
   - Include clear sections: Overview, When to Use, Guidelines
   - Provide code examples with multiple languages when applicable
   - Include best practices and anti-patterns
   - Add relevant resources and links

5. **Test your skill**:
   - Ensure markdown is properly formatted
   - Verify all code examples are correct
   - Check that links are valid
   - Test with an AI agent if possible

6. **Update the main README.md**:
   - Add your skill to the "Available Skills" section
   - Keep the list alphabetically sorted

7. **Commit your changes**:
   ```bash
   git add .
   git commit -m "Add [skill-name] skill"
   ```

8. **Push and create a pull request**:
   ```bash
   git push origin feature/my-new-skill
   ```

### Improving Existing Skills

1. Fork the repository and create a branch
2. Make your improvements:
   - Fix typos or unclear explanations
   - Add missing examples or edge cases
   - Update outdated information
   - Improve code samples
3. Submit a pull request with a clear description of changes

## Skill Quality Guidelines

### Content Requirements

- **Clear and Concise**: Explain concepts clearly without unnecessary jargon
- **Practical Examples**: Include real-world, working code examples
- **Multi-Language**: Support multiple programming languages when relevant
- **Complete**: Cover common use cases and edge cases
- **Current**: Use up-to-date libraries and best practices

### Structure Requirements

- **YAML Frontmatter**: Must include `name` and `description`
- **Sections**: Should include Overview, When to Use, and detailed guidelines
- **Code Blocks**: Use proper syntax highlighting
- **Resources**: Link to official documentation and relevant resources

### Style Guidelines

- Use heading levels consistently (H1 for title, H2 for main sections, H3 for subsections)
- Keep code examples focused and relevant
- Use ✓ and ✗ for do's and don'ts
- Format consistently with existing skills
- Keep line length reasonable (aim for 80-100 characters for readability)

## Code of Conduct

### Our Standards

- Be respectful and inclusive
- Accept constructive criticism gracefully
- Focus on what's best for the community
- Show empathy towards others

### Unacceptable Behavior

- Harassment or discriminatory language
- Personal attacks or insults
- Publishing others' private information
- Any conduct that could reasonably be considered inappropriate

## Review Process

1. **Submission**: Create a pull request with your changes
2. **Initial Review**: Maintainers will review for basic requirements
3. **Feedback**: You may receive requests for changes or improvements
4. **Testing**: Skills may be tested with actual AI agents
5. **Approval**: Once approved, your PR will be merged

## Questions?

If you have questions about contributing:

- Open an issue with the "question" label
- Check existing issues and discussions
- Review the README.md for general information

## Recognition

Contributors will be recognized in the project:

- GitHub contributors page
- Special acknowledgment for significant contributions
- Your name in the AUTHORS file (if desired)

Thank you for helping make AI agents smarter and more capable!
