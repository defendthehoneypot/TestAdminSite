# Getting Started

This guide will help you get started with TestAdminSite documentation.

## Prerequisites

Before you begin, ensure you have the following installed:

- Python 3.8 or higher
- pip (Python package manager)
- Git

## Local Development

To run this documentation site locally:

### 1. Clone the Repository

```bash
git clone https://github.com/defendthehoneypot/TestAdminSite.git
cd TestAdminSite
```

### 2. Install Dependencies

```bash
pip install -r requirements.txt
```

### 3. Run the Development Server

```bash
mkdocs serve
```

The documentation site will be available at `http://127.0.0.1:8000/`

### 4. Build the Site

To build the static site:

```bash
mkdocs build
```

This will create a `site/` directory with the built documentation.

## Making Changes

1. Edit the Markdown files in the `docs/` directory
2. The development server will automatically reload changes
3. Preview your changes in the browser
4. Commit and push when satisfied

## Next Steps

- Learn about [Installation](documentation/installation.md)
- Review [Configuration](documentation/configuration.md) options
- Set up your [Development Environment](development/setup.md)
- Read the [Contributing Guide](development/contributing.md)
