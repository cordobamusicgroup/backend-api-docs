# CMG Backend API Documentation

Complete API documentation for the Córdoba Music Group royalty management platform.

- **📖 Live Site:** https://cordobamusicgroup.github.io/backend-api-docs
- **🏗️ Built with:** MkDocs + Material Theme
- **🚀 Auto-deploy:** GitHub Pages + GitHub Actions

## Quick Start

### Local Development

```bash
# Install dependencies
pip install -r requirements.txt

# Run local server
mkdocs serve

# Open http://localhost:8000
```

### Deploy

Just push to `main` branch:

```bash
git add .
git commit -m "docs: your message"
git push origin main
```

Site automatically updates in ~1 minute via GitHub Actions.

## Documentation Structure

```
docs/
├── index.md              # Home page
├── guides/               # Tutorials & guides
│   ├── getting-started.md
│   ├── authentication.md
│   ├── best-practices.md
│   ├── examples.md
│   ├── data-reference.md
│   ├── quick-reference.md
│   ├── deployment.md
│   └── local-development.md
└── api/                  # API Reference
    ├── index.md
    ├── financial-reports.md
    └── music-distribution.md
```

## Configuration Files

- **`mkdocs.yml`** - MkDocs configuration
- **`requirements.txt`** - Python dependencies
- **`.github/workflows/deploy.yml`** - GitHub Actions CI/CD

## Key Features

✨ **Material Theme** - Modern, responsive design  
🔍 **Search** - Full-text search across all docs  
🌙 **Dark Mode** - Automatic light/dark theme  
⚡ **Fast** - ~0.5s build time  
📱 **Mobile** - Fully responsive  
🔄 **Auto-deploy** - CI/CD with GitHub Actions

## Writing Documentation

All documentation is in Markdown (.md files) in the `docs/` directory.

### Adding a New Page

1. Create `.md` file in appropriate folder
2. Add to `mkdocs.yml` navigation
3. Push to `main` → auto-deployed

### Markdown Features

- Code blocks with syntax highlighting
- Tables, lists, admonitions
- Mermaid diagrams
- LaTeX math equations
- Tabs for multi-language examples

See [MkDocs Documentation](https://www.mkdocs.org/) for more.

## Support

For issues or questions about the documentation, please contact your administrator.

---

**Last Updated:** December 15, 2025  
**API Version:** 1.0.0
