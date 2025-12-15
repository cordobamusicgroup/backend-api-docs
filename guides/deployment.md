---
layout: default
title: Deployment & CI/CD
---

# Deployment & CI/CD

This documentation is automatically deployed to GitHub Pages using GitHub Actions.

## 🚀 Automatic Deployment

### How It Works

Every time you push changes to the repository, GitHub Actions automatically:

1. **Detects** Markdown file changes
2. **Builds** the site using Jekyll
3. **Deploys** to GitHub Pages
4. **Updates** the live documentation

### Trigger Events

The automatic deployment is triggered when:

- ✅ **Any Markdown file** (`.md`) is modified
- ✅ **Jekyll config** (`_config.yml`) is updated
- ✅ **Workflow file** is changed
- ✅ **Manual trigger** via workflow_dispatch

The deployment only happens on:

- Push to `main` or `master` branch
- Successfully built without errors

### Branches

| Branch             | Deployment    | Purpose                |
| ------------------ | ------------- | ---------------------- |
| `main` / `master`  | ✅ Yes        | Production deployment  |
| `staging` / others | 🔄 Build only | Testing, no deployment |

---

## 📋 GitHub Actions Workflow

### File Location

```
.github/workflows/deploy.yml
```

### Workflow Steps

1. **Checkout** - Clone repository
2. **Setup Pages** - Configure GitHub Pages
3. **Setup Ruby** - Install Ruby 3.3
4. **Build** - Compile Markdown with Jekyll
5. **Upload** - Prepare artifact
6. **Deploy** - Publish to GitHub Pages

### Status Checks

Check deployment status:

1. Go to repository → **Actions** tab
2. View latest workflow run
3. Check build and deployment logs

---

## 🔧 Configuration

### Workflow File: `.github/workflows/deploy.yml`

```yaml
name: Build and Deploy GitHub Pages

on:
  push:
    branches: [main, master]
    paths:
      - "**.md" # Only build on .md changes
      - "_config.yml"
      - ".github/workflows/deploy.yml"
  pull_request: # Also test on PRs
    branches: [main, master]
  workflow_dispatch: # Manual trigger

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  build: # Build Jekyll site
  deploy: # Deploy to GitHub Pages
```

### Jekyll Config: `_config.yml`

```yaml
theme: jekyll-theme-minimal
plugins:
  - jekyll-default-layout
  - jekyll-seo-tag
  - jekyll-relative-links

markdown: kramdown
```

---

## 📝 Making Changes

### Add a New Documentation Page

1. **Create** a new `.md` file in `guides/` or `api/`:

   ```
   guides/new-guide.md
   ```

2. **Add front matter** at the top:

   ```markdown
   ---
   layout: default
   title: New Guide
   ---
   ```

3. **Commit** to `main` branch:

   ```bash
   git add guides/new-guide.md
   git commit -m "Add new documentation guide"
   git push origin main
   ```

4. **Automatic deployment** starts immediately

### Update Existing Documentation

1. **Edit** the `.md` file
2. **Commit** and **push** to `main`
3. **GitHub Actions** automatically rebuilds and deploys

### Update Jekyll Configuration

1. **Edit** `_config.yml`
2. **Commit** and **push**
3. **Automatic rebuild** with new settings

---

## ✅ Verification

### Check Deployment Status

**In GitHub:**

```
Repository → Actions → Latest Workflow Run
```

**Expected output:**

- ✅ build - Successful
- ✅ deploy - Successful

### View Live Site

After successful deployment:

```
https://cordobamusicgroup.github.io/backend-api-docs
```

Wait 1-2 minutes for changes to appear.

### Check for Errors

If deployment fails:

1. Go to **Actions** tab
2. Click failed workflow
3. Expand job logs
4. Look for error messages

Common errors:

- **Jekyll syntax** - Fix Markdown formatting
- **Missing layout** - Ensure front matter has `layout: default`
- **Relative links** - Use correct path format

---

## 🔄 Build vs Deploy

### Build Phase

- Runs on: All pushes to any branch
- Tests: Jekyll compilation
- Result: Build artifact
- Purpose: Validation

### Deploy Phase

- Runs on: Successful build to `main`/`master`
- Action: Publish to GitHub Pages
- URL: https://cordobamusicgroup.github.io/backend-api-docs
- Status: Available immediately

---

## 🚨 Troubleshooting

### Site Not Updating

**Problem:** Changes pushed but site not updated

**Solutions:**

1. Check **Actions** tab for failed workflows
2. Wait 2-3 minutes for deployment
3. Clear browser cache (Ctrl+Shift+Del)
4. Check if pushed to `main` branch (not other branches)

### Build Failed

**Problem:** Workflow shows failed build

**Solutions:**

1. Check error in workflow logs
2. Validate Markdown syntax
3. Verify front matter is correct
4. Ensure images/links are valid paths

### Deploy Failed

**Problem:** Build succeeded but deploy failed

**Solutions:**

1. Check GitHub Pages settings (usually auto-configured)
2. Verify branch is `main` or `master`
3. Check repository permissions
4. Try manual re-run from Actions

---

## 🔐 GitHub Pages Settings

### Auto-configured by Workflow

The workflow automatically configures:

- ✅ Source: GitHub Actions
- ✅ Branch: N/A (uses artifacts)
- ✅ Directory: / (root)
- ✅ Domain: cordobamusicgroup.github.io/backend-api-docs

### Manual Verification (if needed)

1. Go to **Settings** → **Pages**
2. Verify:
   - Source: GitHub Actions
   - Status: Deployed

---

## 📊 Workflow Metrics

### Performance

- **Build time:** ~30-60 seconds
- **Deploy time:** ~10-20 seconds
- **Total:** ~1-2 minutes

### Storage

- **Site size:** ~500KB
- **Artifact retention:** 90 days

---

## 🛠️ Advanced: Manual Deployment

### Trigger Manually

1. Go to repository → **Actions**
2. Select **Build and Deploy GitHub Pages**
3. Click **Run workflow**
4. Select branch: `main`
5. Click **Run workflow**

### Re-run Failed Job

1. Go to failed workflow run
2. Click **Re-run all jobs**
3. Wait for build to complete

---

## 📚 Related Files

- [Workflow File](.github/workflows/deploy.yml)
- [Jekyll Config](_config.yml)
- [README](README.md)
- [Main Documentation](index.md)

---

## 🔗 Resources

- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [GitHub Pages Documentation](https://docs.github.com/en/pages)
- [Jekyll Documentation](https://jekyllrb.com/docs/)

---

**Last Updated:** December 15, 2025

**Questions?** Contact your documentation maintainer.
