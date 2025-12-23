# Development Setup

This guide will help you set up your development environment for contributing to TestAdminSite documentation.

## Prerequisites

Ensure you have the following installed:

- **Python 3.8+**: Required for MkDocs
- **pip**: Python package manager
- **Git**: Version control
- **Text Editor/IDE**: VS Code, PyCharm, or your preferred editor

## Initial Setup

### 1. Fork and Clone

```bash
# Fork the repository on GitHub, then clone your fork
git clone https://github.com/YOUR-USERNAME/TestAdminSite.git
cd TestAdminSite

# Add upstream remote
git remote add upstream https://github.com/defendthehoneypot/TestAdminSite.git
```

### 2. Create Virtual Environment

```bash
# Create virtual environment
python -m venv venv

# Activate it
# On Linux/macOS:
source venv/bin/activate
# On Windows:
venv\Scripts\activate
```

### 3. Install Dependencies

```bash
# Install all dependencies
pip install -r requirements.txt

# Verify installation
mkdocs --version
```

## Development Workflow

### Starting the Development Server

```bash
# Start server with live reload
mkdocs serve
```

The site will be available at `http://127.0.0.1:8000/`

The server automatically reloads when you save changes to files.

### Project Structure

```
TestAdminSite/
├── docs/                  # Documentation source files
│   ├── index.md          # Homepage
│   ├── getting-started.md
│   ├── documentation/    # Documentation section
│   └── development/      # Development guides
├── mkdocs.yml            # MkDocs configuration
├── requirements.txt      # Python dependencies
└── README.md             # Project README
```

### Making Changes

1. **Create a Branch**

   ```bash
   git checkout -b feature/your-feature-name
   ```

2. **Edit Documentation**

   - Edit Markdown files in the `docs/` directory
   - Preview changes in your browser
   - Test all links and code examples

3. **Add New Pages**

   - Create new `.md` files in `docs/`
   - Add to navigation in `mkdocs.yml`

   Example:
   ```yaml
   nav:
     - Home: index.md
     - New Section:
         - New Page: section/new-page.md
   ```

4. **Test Your Changes**

   ```bash
   # Build the site
   mkdocs build
   
   # Check for warnings
   mkdocs build --strict
   ```

## Writing Documentation

### Markdown Basics

Use standard Markdown syntax:

```markdown
# Heading 1
## Heading 2
### Heading 3

**Bold text**
*Italic text*

- Bullet list
- Item 2

1. Numbered list
2. Item 2

[Link text](https://example.com)

![Image alt text](path/to/image.png)
```

### Code Blocks

Use fenced code blocks with syntax highlighting:

````markdown
```python
def example():
    return "Hello, World!"
```

```bash
mkdocs serve
```
````

### Admonitions

Create highlighted boxes:

```markdown
!!! note
    This is a note

!!! warning
    This is a warning

!!! tip
    This is a helpful tip

!!! danger
    This is a danger warning
```

### Internal Links

Link to other documentation pages:

```markdown
See the [Installation Guide](../documentation/installation.md)
```

## Testing

### Local Testing

Before submitting changes:

1. **Build the site**
   ```bash
   mkdocs build --clean --strict
   ```

2. **Check for broken links**
   - Manually test internal links
   - Verify external links work

3. **Test in multiple browsers**
   - Chrome/Edge
   - Firefox
   - Safari (if available)

4. **Test responsive design**
   - Desktop view
   - Tablet view
   - Mobile view

### Validation

Run these checks:

```bash
# Build with strict mode (treats warnings as errors)
mkdocs build --strict

# Serve and check console for errors
mkdocs serve
```

## Best Practices

### Documentation Style

1. **Be Clear and Concise**: Use simple language
2. **Use Examples**: Include code examples where helpful
3. **Structure Content**: Use headings and lists
4. **Keep it Updated**: Ensure information is current
5. **Test Instructions**: Verify all steps work

### File Organization

1. Group related content in subdirectories
2. Use descriptive filenames (e.g., `getting-started.md`)
3. Keep filenames lowercase with hyphens
4. Don't nest directories too deeply

### Git Workflow

1. Keep commits small and focused
2. Write clear commit messages
3. Pull upstream changes regularly
4. Test before pushing

## Useful Commands

```bash
# Start development server
mkdocs serve

# Build the site
mkdocs build

# Build and clean old files
mkdocs build --clean

# Strict mode (warnings as errors)
mkdocs build --strict

# Serve on custom port
mkdocs serve --dev-addr=0.0.0.0:8080

# Get help
mkdocs --help
```

## Troubleshooting

### Server Won't Start

Check if port 8000 is in use:

```bash
# Linux/macOS
lsof -i :8000

# Use different port
mkdocs serve --dev-addr=127.0.0.1:8001
```

### Changes Not Appearing

1. Hard refresh browser (Ctrl+F5 or Cmd+Shift+R)
2. Clear browser cache
3. Restart the development server

### Build Failures

1. Check YAML syntax in `mkdocs.yml`
2. Verify all referenced files exist
3. Look for special characters in filenames
4. Run with `--strict` flag for detailed errors

## Next Steps

- Read the [Contributing Guide](contributing.md)
- Check out [Configuration](../documentation/configuration.md) options
- Review existing documentation for style examples

## Getting Help

If you need help:

- Check the [MkDocs documentation](https://www.mkdocs.org/)
- Review [Material theme docs](https://squidfunk.github.io/mkdocs-material/)
- Ask in GitHub Discussions
- Open an issue for bugs

---

*Happy documenting! 📝*
