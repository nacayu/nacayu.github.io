# nacayu-wiki-001 Specification

## 1. System Overview

**System Name:** nacayu-wiki  
**Type:** Embedded personal knowledge management system  
**Core Functionality:** GitHub Pages integrated Wiki with public/private content separation  
**Target Users:** External visitors (public content only), Owner (full content)

## 2. Architecture

### 2.1 Directory Structure

```
nacayu.github.io/
├── _quarto.yml
├── index.qmd, about.qmd, cv.qmd
├── blog/
├── wiki/                          # NEW
│   ├── index.qmd                  # Wiki index page
│   ├── public/                   # Public wiki content
│   │   ├── _metadata.yml          # default: visibility: public
│   │   └── *.qmd                 # Public wiki files
│   └── private/                  # Private wiki content
│       ├── _metadata.yml          # default: visibility: private
│       └── *.qmd                 # Private wiki files
├── scripts/
│   └── filter-wiki.py            # NEW - filter script
├── docs/                         # Build output (GitHub Pages)
└── .github/
    └── workflows/
        └── publish.yml          # Modified for filtering
```

### 2.2 Frontmatter Format

```yaml
---
title: "Page Title"
date: "2025-05-20"
visibility: public      # public | private
tags: [tag1, tag2]
summary: "Brief description"
---
```

### 2.3 Visibility Rules

1. **File-level:** `visibility` field in frontmatter
2. **Directory-level:** `_metadata.yml` sets default for directory
3. **Precedence:** File-level overrides directory-level

## 3. Components

### 3.1 filter-wiki.py

**Purpose:** Scan wiki/ directory and filter files based on visibility

**Input:** `wiki/` directory path

**Output:** List of .qmd files to include (public only)

**Algorithm:**
```
1. Walk wiki/ directory recursively
2. For each .qmd file:
   a. Parse frontmatter
   b. If 'visibility: private' found -> skip
   c. If in private/ directory AND no frontmatter -> skip (use _metadata.yml default)
   d. Otherwise -> include
3. Output list of included files
```

**Error Handling:**
- Malformed YAML -> log warning, skip file
- Missing required fields -> use defaults

### 3.2 GitHub Actions Workflow

**Modified File:** `.github/workflows/publish.yml`

**Changes:**
1. Before `quarto render`: Run `filter-wiki.py` to generate exclusion list
2. Pass exclusion list to `quarto render` (or pre-delete private files)
3. Only public content gets rendered to `docs/`

### 3.3 Wiki Index Page

**File:** `wiki/index.qmd`

**Content:**
- Title: "Wiki"
- List of all public wiki pages with:
  - Link to page
  - One-line summary (from frontmatter `summary` field)
  - Tags
  - Date

## 4. Acceptance Criteria

| ID | Criteria | Verification Method |
|----|----------|---------------------|
| AC1 | filter-wiki.py correctly identifies public files | Unit test |
| AC2 | filter-wiki.py correctly identifies private files | Unit test |
| AC3 | _metadata.yml directory defaults work | Unit test |
| AC4 | CI build output contains no private files | Integration test |
| AC5 | Local wiki/ contains full content | Manual check |
| AC6 | GitHub Pages shows only public content | Browser test |
| AC7 | Wiki index page displays all public pages | Visual check |

## 5. Tech Stack

- **Quarto:** Static site generator
- **Python:** filter-wiki.py implementation
- **frontmatter:** Python YAML frontmatter parsing
- **GitHub Actions:** CI/CD
- **Git:** Version control

## 6. Out of Scope

- Wiki search functionality
- Knowledge graph visualization
- Multi-language support
- API endpoints
- Real-time collaboration