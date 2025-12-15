---
layout: default
title: Local Development
---

# Local Development Setup

Instructions for running the documentation site locally on your computer.

## 📋 Prerequisites

Before starting, ensure you have installed:

- **Ruby** 3.0 or higher
  - Windows: Download from [ruby-lang.org](https://www.ruby-lang.org/en/downloads/)
  - macOS: `brew install ruby`
  - Linux: `apt-get install ruby` or `yum install ruby`

- **Bundler** (Ruby dependency manager)
  ```bash
  gem install bundler
  ```

- **Git** (for cloning the repository)

## 🚀 Quick Start

### 1. Clone Repository

```bash
git clone https://github.com/cordobamusicgroup/backend-api-docs.git
cd backend-api-docs
```

### 2. Install Dependencies

```bash
bundle install
```

This installs:
- Jekyll (~4.3)
- Theme and plugins
- All required gems

### 3. Run Local Server

```bash
bundle exec jekyll serve
```

**Expected output:**
```
Configuration file: /path/to/_config.yml
            Source: /path/to/
       Destination: /path/to/_site
 Incremental build: enabled
      Generating...
                    done in X.XX seconds.
 Auto-regeneration: enabled
    Server address: http://127.0.0.1:4000/backend-api-docs
  Server running... press ctrl-c to stop.
```

### 4. Open in Browser

Visit: `http://localhost:4000/backend-api-docs`

## 📝 Editing Documentation

### Make Changes

1. Edit any `.md` file in `guides/` or `api/` directories
2. Save the file
3. Jekyll automatically regenerates the site (~1-2 seconds)
4. Refresh browser to see changes

### Adding New Pages

Create a new `.md` file with front matter:

```markdown
---
layout: default
title: Page Title
---

# Page Title

Your content here...
```

Add link to navigation in `index.md` or relevant guide files.

### Adding Images

1. Create `assets/images/` folder if needed
2. Place image file in folder
3. Reference in Markdown:
   ```markdown
   ![Alt text](./assets/images/filename.png)
   ```

## 🔧 Customization

### Site Configuration

Edit `_config.yml` to customize:

```yaml
title: CMG Backend API Documentation
description: API documentation
theme: jekyll-theme-minimal
baseurl: "/backend-api-docs"
```

### Jekyll Plugins

Configured plugins in `_config.yml`:

- `jekyll-theme-minimal` - Minimal theme
- `jekyll-default-layout` - Default page layout
- `jekyll-seo-tag` - SEO optimization
- `jekyll-relative-links` - Relative link conversion

### CSS Customization

Create `assets/css/style.scss` to override theme styles:

```scss
---
---

@import "jekyll-theme-minimal";

// Custom styles here
body {
  color: #333;
}
```

## 🐛 Troubleshooting

### Port Already in Use

If port 4000 is already in use:

```bash
bundle exec jekyll serve --port 4001
```

Then visit: `http://localhost:4001/backend-api-docs`

### Gems Not Found

Reinstall dependencies:

```bash
bundle update
bundle install
```

### Changes Not Showing

1. Check terminal for build errors
2. Verify file is saved
3. Hard refresh browser (Ctrl+Shift+R)
4. Check `_site/` folder for compiled output

### Jekyll Not Found

Install Jekyll globally:

```bash
gem install jekyll bundler
```

Then run:
```bash
bundle install
```

## 📊 Project Structure

```
backend-api-docs/
├── .github/
│   └── workflows/
│       └── deploy.yml           # CI/CD pipeline
├── guides/
│   ├── getting-started.md
│   ├── authentication.md
│   ├── best-practices.md
│   ├── examples.md
│   ├── data-reference.md
│   ├── quick-reference.md
│   ├── deployment.md
│   └── local-development.md
├── api/
│   ├── README.md
│   ├── financial-reports.md
│   └── music-distribution.md
├── assets/
│   └── images/               # Images for docs
├── _config.yml               # Jekyll configuration
├── _site/                    # Generated site (ignore)
├── index.md                  # Home page
├── README.md                 # Repository readme
├── Gemfile                   # Ruby dependencies
├── Gemfile.lock              # Locked versions
├── LICENSE
└── .gitignore
```

## 🔄 Workflow

### Typical Development Flow

```bash
# Start server
bundle exec jekyll serve

# In another terminal, make changes:
# - Edit .md files
# - Refresh browser to see changes
# - Preview before pushing

# When satisfied:
git add .
git commit -m "docs: update API documentation"
git push origin main

# GitHub Actions automatically:
# - Builds site
# - Deploys to GitHub Pages
```

## 📦 Building for Production

Generate static site:

```bash
bundle exec jekyll build
```

Output in `_site/` folder ready to deploy.

## 🔐 Security

### Never Commit

- Ruby gems (.gem files)
- Environment files (.env)
- API tokens or credentials
- Generated site (_site/)

Use `.gitignore` to prevent accidental commits.

## 📚 Useful Commands

```bash
# Install dependencies
bundle install

# Update dependencies
bundle update

# Run local server
bundle exec jekyll serve

# Build site
bundle exec jekyll build

# Build with drafts
bundle exec jekyll serve --drafts

# Clean build
bundle exec jekyll clean
```

## 🆘 Getting Help

### Common Issues

1. **Ruby version issues** - Check Ruby version: `ruby --version`
2. **Permission denied** - Use `bundle exec` prefix
3. **Gems not found** - Run `bundle install` again

### Resources

- [Jekyll Documentation](https://jekyllrb.com/docs/)
- [Markdown Guide](https://www.markdownguide.org/)
- [GitHub Pages Docs](https://docs.github.com/en/pages)

## 📝 Next Steps

1. Make your changes to Markdown files
2. Preview locally
3. Commit to Git
4. Push to `main` or `master` branch
5. GitHub Actions automatically deploys!

---

**Ready to start?** Run: `bundle install && bundle exec jekyll serve`

Then visit: `http://localhost:4000/backend-api-docs`
