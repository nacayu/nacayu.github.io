# Zedong Yu's Personal Website

Personal website powered by [Quarto](https://quarto.org/) and deployed on GitHub Pages.

## Website URL
https://nacayu.github.io

## Setup Instructions

### Prerequisites
- GitHub account
- Git installed locally

### Current Setup

The `master` branch is the source branch. GitHub Actions renders the Quarto site and publishes it to `gh-pages` after every push to `master`.

### Enable GitHub Pages
1. Go to repository **Settings → Pages**
2. Under "Build and deployment", set **Source** to `gh-pages`
3. Wait a few minutes for deployment

## Adding Content

### Edit existing pages
- `index.qmd` - Home page
- `about.qmd` - About page
- `cv.qmd` - CV page
- `blog/index.qmd` - Blog listing

### Create new blog posts
Create files in `blog/` directory with naming convention `YYYY-MM-DD-title.qmd`:

```markdown
---
title: "My First Post"
date: 2025-05-20
---

Your content here...
```

### Render locally
```bash
quarto render
```

## Project Structure
```
├── _quarto.yml          # Quarto configuration
├── index.qmd            # Home page
├── about.qmd            # About page
├── cv.qmd               # CV page
├── blog/
│   └── index.qmd        # Blog listing
├── .github/
│   └── workflows/
│       └── publish.yml  # GitHub Actions
└── .gitignore
```
