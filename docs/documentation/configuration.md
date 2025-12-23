# Configuration

This guide explains how to configure the MkDocs site for TestAdminSite.

## Configuration File

The main configuration file is `mkdocs.yml` located in the root directory. This file controls all aspects of the documentation site.

## Basic Configuration

### Site Information

```yaml
site_name: TestAdminSite
site_description: A development MkDocs site for testing and documentation
site_author: TestAdminSite Team
site_url: https://defendthehoneypot.github.io/TestAdminSite/
```

- **site_name**: The name of your site
- **site_description**: A brief description for SEO
- **site_author**: The author or team name
- **site_url**: The URL where the site will be hosted

## Theme Configuration

### Material Theme

This site uses the Material for MkDocs theme with customizations:

```yaml
theme:
  name: material
  palette:
    - scheme: default
      primary: indigo
      accent: indigo
    - scheme: slate
      primary: indigo
      accent: indigo
```

### Color Schemes

You can customize the color scheme:

- **primary**: Main color (indigo, blue, red, etc.)
- **accent**: Accent color for interactive elements
- **scheme**: Color palette (default/slate for light/dark mode)

### Features

Available features:

```yaml
features:
  - navigation.tabs
  - navigation.sections
  - navigation.expand
  - navigation.top
  - search.suggest
  - search.highlight
  - content.code.copy
```

## Navigation Structure

Define your site's navigation in the `nav` section:

```yaml
nav:
  - Home: index.md
  - Getting Started: getting-started.md
  - Documentation:
      - Overview: documentation/overview.md
      - Installation: documentation/installation.md
  - Development:
      - Setup: development/setup.md
```

## Markdown Extensions

Enable advanced Markdown features:

### Code Highlighting

```yaml
markdown_extensions:
  - pymdownx.highlight:
      anchor_linenums: true
  - pymdownx.inlinehilite
  - pymdownx.snippets
```

### Admonitions

Create note boxes and warnings:

```yaml
markdown_extensions:
  - admonition
  - pymdownx.details
  - pymdownx.superfences
```

Example usage:

```markdown
!!! note
    This is a note box

!!! warning
    This is a warning box
```

### Emojis

Enable emoji support:

```yaml
markdown_extensions:
  - pymdownx.emoji:
      emoji_index: !!python/name:material.extensions.emoji.twemoji
      emoji_generator: !!python/name:material.extensions.emoji.to_svg
```

## Plugins

### Search Plugin

Enable site-wide search:

```yaml
plugins:
  - search
```

### Tags Plugin

Organize content with tags:

```yaml
plugins:
  - tags
```

## Extra Configuration

### Social Links

Add social media links:

```yaml
extra:
  social:
    - icon: fontawesome/brands/github
      link: https://github.com/defendthehoneypot/TestAdminSite
    - icon: fontawesome/brands/twitter
      link: https://twitter.com/yourusername
```

## Development Server Options

Run the development server with custom options:

```bash
# Default port (8000)
mkdocs serve

# Custom port
mkdocs serve --dev-addr=0.0.0.0:8080

# Watch specific files
mkdocs serve --watch docs/

# Strict mode (warnings as errors)
mkdocs serve --strict
```

## Build Configuration

Build the static site:

```bash
# Standard build
mkdocs build

# Clean build (remove old files)
mkdocs build --clean

# Strict mode
mkdocs build --strict
```

## Advanced Configuration

### Custom CSS

Add custom styles:

1. Create `docs/stylesheets/extra.css`
2. Add to `mkdocs.yml`:

```yaml
extra_css:
  - stylesheets/extra.css
```

### Custom JavaScript

Add custom scripts:

1. Create `docs/javascripts/extra.js`
2. Add to `mkdocs.yml`:

```yaml
extra_javascript:
  - javascripts/extra.js
```

## Environment-Specific Configuration

You can create multiple configuration files:

- `mkdocs.yml` - Production configuration
- `mkdocs-dev.yml` - Development configuration

Use with:

```bash
mkdocs serve -f mkdocs-dev.yml
```

## Best Practices

1. **Keep it Simple**: Start with basic configuration
2. **Version Control**: Commit `mkdocs.yml` to git
3. **Test Changes**: Preview locally before deploying
4. **Document Changes**: Comment complex configurations
5. **Use Variables**: Define reusable values at the top

## Troubleshooting

### Build Errors

If you encounter build errors:

1. Check YAML syntax (indentation matters!)
2. Verify file paths in `nav` section
3. Ensure all referenced files exist
4. Run `mkdocs build --strict` for detailed errors

### Theme Issues

If the theme doesn't look right:

1. Clear browser cache
2. Verify theme installation: `pip show mkdocs-material`
3. Check for conflicting custom CSS

## Next Steps

- Explore [Development Setup](../development/setup.md)
- Learn about [Contributing](../development/contributing.md)
- Review [MkDocs documentation](https://www.mkdocs.org/)

---

*For more details, see the official [MkDocs configuration documentation](https://www.mkdocs.org/user-guide/configuration/).*
