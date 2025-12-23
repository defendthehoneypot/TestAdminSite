# Contributing

Thank you for your interest in contributing to TestAdminSite! This guide will help you get started.

## Code of Conduct

By participating in this project, you agree to maintain a respectful and inclusive environment for everyone.

## How Can I Contribute?

There are many ways to contribute:

- 📝 **Documentation**: Improve or add documentation
- 🐛 **Bug Reports**: Report issues you encounter
- ✨ **Feature Requests**: Suggest new features
- 🔧 **Code**: Submit bug fixes or new features
- 🎨 **Design**: Improve UI/UX
- 📖 **Examples**: Add examples and tutorials

## Getting Started

### 1. Set Up Your Environment

Follow the [Development Setup](setup.md) guide to prepare your local environment.

### 2. Find Something to Work On

- Check the [issue tracker](https://github.com/defendthehoneypot/TestAdminSite/issues)
- Look for issues labeled `good-first-issue` or `help-wanted`
- Ask in discussions if you're unsure where to start

### 3. Create a Branch

```bash
# Update your fork
git checkout main
git pull upstream main

# Create a feature branch
git checkout -b feature/your-feature-name
```

## Making Changes

### Documentation Changes

1. **Edit Files**: Make changes to Markdown files in `docs/`
2. **Preview**: Run `mkdocs serve` to preview locally
3. **Test**: Check all links and formatting
4. **Commit**: Write clear commit messages

### Adding New Pages

1. Create a new `.md` file in the appropriate directory
2. Add navigation entry in `mkdocs.yml`:

```yaml
nav:
  - Section:
      - New Page: path/to/new-page.md
```

3. Test that navigation works correctly

### Improving Existing Content

- Fix typos and grammatical errors
- Clarify confusing sections
- Add missing information
- Update outdated content
- Improve code examples

## Commit Guidelines

### Commit Message Format

Use clear, descriptive commit messages:

```
[type]: Brief description (50 chars or less)

More detailed explanation if needed. Wrap at 72 characters.
Explain what changes were made and why.

- Bullet points are okay
- Reference issues: Fixes #123
```

### Types

- `docs`: Documentation changes
- `feat`: New features
- `fix`: Bug fixes
- `style`: Formatting, missing semicolons, etc.
- `refactor`: Code restructuring
- `test`: Adding tests
- `chore`: Maintenance tasks

### Examples

```
docs: Add installation guide for Windows

feat: Add dark mode toggle to navigation

fix: Correct broken links in getting started guide

docs: Update configuration examples
```

## Pull Request Process

### 1. Before Submitting

- [ ] Test your changes locally with `mkdocs serve`
- [ ] Build the site without errors: `mkdocs build --strict`
- [ ] Check for broken links
- [ ] Ensure formatting is consistent
- [ ] Update relevant documentation

### 2. Submit Pull Request

1. **Push to Your Fork**

   ```bash
   git push origin feature/your-feature-name
   ```

2. **Open Pull Request**
   - Go to the repository on GitHub
   - Click "New Pull Request"
   - Select your branch
   - Fill out the PR template

3. **PR Description**

   Include:
   - What changes were made
   - Why the changes were needed
   - How to test the changes
   - Screenshots (if applicable)
   - Related issues

### 3. Code Review

- Be responsive to feedback
- Make requested changes promptly
- Ask questions if something is unclear
- Keep the discussion professional

### 4. After Approval

Once approved, a maintainer will merge your PR. Thank you for contributing!

## Style Guide

### Documentation Style

- **Use clear language**: Write for a general audience
- **Be concise**: Get to the point quickly
- **Use examples**: Show, don't just tell
- **Format consistently**: Follow existing patterns
- **Check spelling**: Use a spell checker

### Markdown Style

```markdown
# Use ATX-style headers (with #)

**Bold** for emphasis, *italic* for minor emphasis

Use `code` for inline code and commands

Use fenced code blocks:
```python
# Code here
```

Use relative links: [Link](../page.md)
```

### File Naming

- Use lowercase letters
- Separate words with hyphens
- Use descriptive names
- Examples: `getting-started.md`, `api-reference.md`

## Testing Your Changes

### Local Testing

```bash
# Serve locally
mkdocs serve

# Build with strict mode
mkdocs build --strict

# Clean build
mkdocs build --clean
```

### Browser Testing

Test in multiple browsers:
- Chrome/Edge
- Firefox
- Safari (if available)

### Device Testing

Test responsive design:
- Desktop (1920x1080)
- Tablet (768x1024)
- Mobile (375x667)

## Reporting Bugs

### Before Reporting

1. Search existing issues
2. Verify the bug still exists
3. Collect information about your environment

### Bug Report Template

```markdown
**Describe the bug**
A clear description of what the bug is.

**To Reproduce**
Steps to reproduce:
1. Go to '...'
2. Click on '...'
3. See error

**Expected behavior**
What you expected to happen.

**Screenshots**
If applicable, add screenshots.

**Environment:**
- OS: [e.g., Ubuntu 20.04]
- Python version: [e.g., 3.10]
- MkDocs version: [e.g., 1.5.0]
- Browser: [e.g., Chrome 120]

**Additional context**
Any other relevant information.
```

## Feature Requests

When suggesting a feature:

1. **Check existing requests**: Avoid duplicates
2. **Describe the problem**: What need does it address?
3. **Propose a solution**: How should it work?
4. **Consider alternatives**: Other possible approaches
5. **Provide examples**: Show use cases

## Questions?

If you have questions:

- Check existing documentation
- Search closed issues
- Ask in GitHub Discussions
- Contact maintainers

## Recognition

Contributors are recognized in:

- GitHub contributors list
- Release notes
- Documentation credits

## Thank You!

Your contributions make this project better for everyone. We appreciate your time and effort! 🎉

---

*For technical setup details, see [Development Setup](setup.md).*
