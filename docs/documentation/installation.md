# Installation

This guide covers the installation of TestAdminSite and its documentation system.

## System Requirements

### Minimum Requirements

- **Python**: 3.8 or higher
- **pip**: Latest version
- **Operating System**: Linux, macOS, or Windows
- **RAM**: 2 GB minimum
- **Disk Space**: 500 MB for dependencies

### Recommended Requirements

- **Python**: 3.10 or higher
- **RAM**: 4 GB or more
- **Disk Space**: 1 GB or more

## Installation Methods

### Method 1: Using pip (Recommended)

The simplest way to install the required dependencies:

```bash
# Clone the repository
git clone https://github.com/defendthehoneypot/TestAdminSite.git
cd TestAdminSite

# Install dependencies
pip install -r requirements.txt
```

### Method 2: Using a Virtual Environment

For isolated development:

```bash
# Create a virtual environment
python -m venv venv

# Activate the virtual environment
# On Linux/macOS:
source venv/bin/activate
# On Windows:
venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

### Method 3: Using Poetry

If you prefer Poetry for dependency management:

```bash
# Initialize Poetry (if not already done)
poetry init

# Add dependencies
poetry add mkdocs mkdocs-material pymdown-extensions

# Install
poetry install
```

## Verifying Installation

After installation, verify that MkDocs is installed correctly:

```bash
mkdocs --version
```

You should see output similar to:

```
mkdocs, version 1.5.0 from /path/to/mkdocs (Python 3.10)
```

## Troubleshooting

### Common Issues

#### Issue: Command not found

If you get a "command not found" error:

1. Ensure Python and pip are installed:
   ```bash
   python --version
   pip --version
   ```

2. Check that the installation directory is in your PATH

#### Issue: Permission Denied

If you encounter permission issues:

```bash
# Use --user flag
pip install --user -r requirements.txt
```

#### Issue: Dependency Conflicts

If you have dependency conflicts:

1. Use a virtual environment (Method 2)
2. Upgrade pip: `pip install --upgrade pip`
3. Try installing dependencies one by one

### Getting Help

If you continue to experience issues:

- Check the [MkDocs documentation](https://www.mkdocs.org/)
- Review the [Material theme documentation](https://squidfunk.github.io/mkdocs-material/)
- Open an issue on our [GitHub repository](https://github.com/defendthehoneypot/TestAdminSite/issues)

## Next Steps

After successful installation:

1. Review the [Configuration](configuration.md) guide
2. Set up your [Development Environment](../development/setup.md)
3. Start the development server: `mkdocs serve`

---

*For more information, see the [Getting Started](../getting-started.md) guide.*
