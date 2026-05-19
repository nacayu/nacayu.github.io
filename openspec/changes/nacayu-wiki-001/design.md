# nacayu-wiki-001 Design

## 1. Design Decisions

### 1.1 Why Frontmatter-based Visibility?

**Decision:** Use YAML frontmatter `visibility` field instead of file naming conventions

**Alternatives Considered:**
- File naming (e.g., `_private-*.qmd`) - Less explicit, harder to manage
- Directory-based - Requires moving files to change visibility
- Database/config file - Additional file to maintain

**Selected:** Frontmatter-based

**Rationale:**
- LLM-friendly (easy to read/write)
- Single source of truth in each file
- Directory structure reflects content organization, not access control
- Extensible (can add more metadata later)

### 1.2 Why Python for filter-wiki.py?

**Decision:** Pure Python implementation (no external dependencies except `pyyaml`)

**Rationale:**
- Simple, single-file solution
- Minimal dependencies (just `pyyaml`)
- Easy to understand and maintain
- Can be extended if needed

### 1.3 Why _metadata.yml for Directory Defaults?

**Decision:** Support directory-level defaults via `_metadata.yml`

**Rationale:**
- Standard Quarto/_obsidian pattern
- Easy bulk setting (all files in directory inherit default)
- File-level override when needed
- No surprises for content authors

## 2. Detailed Design

### 2.1 filter-wiki.py Interface

```python
#!/usr/bin/env python3
"""Filter wiki files based on visibility frontmatter."""

import argparse
from pathlib import Path
from typing import List

def parse_args() -> argparse.Namespace:
    parser = argparse.ArgumentParser(description='Filter wiki files by visibility')
    parser.add_argument('wiki_dir', type=Path, help='Wiki directory path')
    parser.add_argument('--output', type=Path, help='Output file for file list')
    return parser.parse_args()

def scan_wiki(wiki_dir: Path) -> List[Path]:
    """Scan wiki directory and return list of public files."""
    ...

def main():
    args = parse_args()
    public_files = scan_wiki(args.wiki_dir)
    if args.output:
        args.output.write_text('\n'.join(str(f) for f in public_files))
    else:
        for f in public_files:
            print(f)

if __name__ == '__main__':
    main()
```

### 2.2 _metadata.yml Format

```yaml
# For public/ directory
visibility: public

# For private/ directory  
visibility: private
```

### 2.3 Frontmatter Parsing

```python
import yaml
from pathlib import Path
from typing import Optional

def parse_frontmatter(file_path: Path) -> dict:
    """Parse YAML frontmatter from .qmd file."""
    content = file_path.read_text()
    
    if content.startswith('---'):
        parts = content.split('---', 2)
        if len(parts) >= 3:
            frontmatter = yaml.safe_load(parts[1])
            return frontmatter or {}
    
    return {}

def get_file_visibility(file_path: Path, dir_default: str = 'public') -> str:
    """Get visibility for a file, considering frontmatter and directory default."""
    meta = parse_frontmatter(file_path)
    
    # File-level override
    if 'visibility' in meta:
        return meta['visibility']
    
    # Use directory default
    return dir_default
```

### 2.4 CI Integration

**Approach:** Modify GitHub Actions workflow to run filter-wiki.py before quarto render

```yaml
# In publish.yml, before quarto render step:
- name: Filter wiki files
  run: |
    python scripts/filter-wiki.py wiki/ --output wiki/public-files.txt
    # Generate list of files to process

- name: Render and Publish
  uses: quarto-dev/quarto-actions/publish@v2
  with:
    target: gh-pages
  env:
    GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

**Alternative Approach (simpler):**
Pre-delete private files before render, restore after

```yaml
- name: Filter wiki (pre-render)
  run: |
    python scripts/filter-wiki.py wiki/ --output /tmp/public-files.txt
    mkdir -p /tmp/wiki-filtered
    # Copy only public files to temporary location
    # Render from temporary location
```

## 3. File Structure

### 3.1 New Files

| File | Purpose |
|------|---------|
| `wiki/index.qmd` | Wiki home page |
| `wiki/public/_metadata.yml` | Public directory defaults |
| `wiki/private/_metadata.yml` | Private directory defaults |
| `scripts/filter-wiki.py` | Main filter script |
| `tests/test_filter_wiki.py` | Unit tests |

### 3.2 Modified Files

| File | Change |
|------|--------|
| `.github/workflows/publish.yml` | Add filter step before render |
| `.gitignore` | Ensure private content excluded |

## 4. Error Handling

| Scenario | Behavior |
|----------|----------|
| Malformed YAML frontmatter | Log warning, skip file (treat as directory default) |
| Missing visibility field | Use `_metadata.yml` default, or 'public' if no default |
| File not found | Log error, continue with other files |
| Empty wiki directory | Output empty list, no error |

## 5. Testing Strategy

### 5.1 Unit Tests

```python
def test_parse_public_file():
    # Given: .qmd file with visibility: public
    # When: scan_wiki() is called
    # Then: file is included in output

def test_parse_private_file():
    # Given: .qmd file with visibility: private
    # When: scan_wiki() is called
    # Then: file is NOT included in output

def test_directory_default_private():
    # Given: file in private/ with no frontmatter
    # When: scan_wiki() is called
    # Then: file is NOT included in output

def test_file_override_directory():
    # Given: file with visibility: public in private/ directory
    # When: scan_wiki() is called
    # Then: file IS included in output
```

### 5.2 Integration Tests

- CI build produces docs/ without private files
- GitHub Pages URL returns 404 for private pages
- Wiki index page links are all accessible