# TestAdminSite Documentation

> **Secure Access Control & VPN Defense Documentation Site**

This repository contains the documentation for TestAdminSite, a development site mimicking cybersecurity documentation similar to VesperSecure.

## 🚀 Quick Start  

### Prerequisites
- Python 3.8 or higher
- pip

### Installation

```bash
# Clone the repository
git clone https://github.com/defendthehoneypot/TestAdminSite.git
cd TestAdminSite

# Install dependencies
pip install -r requirements.txt

# Run the development server
mkdocs serve
```

Visit `http://127.0.0.1:8000` to view the documentation site.

## 📚 Building the Documentation

```bash
# Build static site
mkdocs build

# Build with strict mode (warnings as errors)
mkdocs build --strict
```

The built site will be in the `site/` directory.

## 🏗️ Project Structure

```
TestAdminSite/
├── docs/                  # Documentation source files
│   ├── index.md          # Homepage
│   ├── getting-started.md
│   ├── documentation/    # User guide
│   ├── administration/   # Admin guides
│   ├── api/             # API reference
│   ├── resources/       # FAQ, best practices
│   └── development/     # Dev guides
├── mkdocs.yml           # MkDocs configuration
├── requirements.txt     # Python dependencies
└── README.md           # This file
```

## 🎨 Theme

This site uses the Material for MkDocs theme with:
- Dark/Light mode toggle
- Deep purple color scheme
- Shield lock icon
- Advanced navigation features
- Code syntax highlighting
- Search functionality

## 📝 Contributing

Contributions are welcome! Please see the [Contributing Guide](docs/development/contributing.md) for details.

## 📄 License

This is a development/testing documentation site.

## 🔗 Links

- [Documentation](https://defendthehoneypot.github.io/TestAdminSite/)
- [GitHub Repository](https://github.com/defendthehoneypot/TestAdminSite)
- [MkDocs](https://www.mkdocs.org/)
- [Material Theme](https://squidfunk.github.io/mkdocs-material/)
