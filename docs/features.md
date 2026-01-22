# Features

This guide explains the plugins and features enabled on aiml.engineer.

## Git Revision Date

Shows "Last updated" and "Created" dates on each page automatically.

### How It Works

The `mkdocs-git-revision-date-localized-plugin` extracts dates from git commit history:

- **Last updated:** Date of the most recent commit that modified the page
- **Created:** Date of the first commit that created the page

### Display Format

Dates are shown in human-readable format:

- "2 days ago"
- "3 weeks ago"
- "2 months ago"

### Fallback Behavior

If git history is unavailable (new files, shallow clones), it falls back to the build date.

### Configuration

```yaml
plugins:
  - git-revision-date-localized:
      enable_creation_date: true
      type: timeago
      fallback_to_build_date: true
```

### Why It's Useful

- Readers know if content is current or outdated
- Contributors see when pages were last maintained
- Encourages keeping documentation up-to-date

## Search

Built-in search functionality powered by MkDocs search plugin.

### Features

- **Live suggestions** - Results appear as you type
- **Highlighting** - Search terms highlighted on the page
- **Shareable URLs** - Search queries included in URL
- **Smart ranking** - Prioritizes title matches over content matches

### Configuration

```yaml
plugins:
  - search:
      lang: en
      separator: '[\s\-\.]+'
```

The separator configuration helps split words on spaces, hyphens, and dots for better search results.

### Search Index

Generated at build time in `site/search/search_index.json`:

- Contains all page titles and content
- Optimized for fast client-side search
- Automatically updated on each build

### Usage

Press `/` or click the search icon to open search. Type your query and press Enter to navigate to results.

## Minification

Automatically minifies HTML, CSS, and JavaScript for faster page loads.

### How It Works

The `mkdocs-minify-plugin` processes files during build:

1. **HTML minification** - Removes comments, whitespace, empty attributes
2. **CSS minification** - Removes comments, whitespace, optimizes selectors
3. **JS minification** - Removes comments, whitespace, shortens variable names

### Configuration

```yaml
plugins:
  - minify:
      minify_html: true
      minify_js: true
      minify_css: true
      htmlmin_opts:
        remove_comments: true
        remove_empty_space: true
      cache_safe: true
```

### Performance Impact

- **File size reduction:** ~30-50%
- **Page load improvement:** ~20-40% faster
- **Build time impact:** +1-2 seconds

### Cache Safety

`cache_safe: true` ensures minified files can be cached by CDNs and browsers without issues.

## Code Highlighting

Syntax highlighting for code blocks using Pygments.

### Supported Languages

100+ programming languages including:

- Python, JavaScript, TypeScript, Rust, Go, Java, C++
- JSON, YAML, TOML, Markdown
- Bash, PowerShell, SQL
- And many more

### Features

- **Line numbers** - Optional line numbers for code blocks
- **Copy button** - One-click copy for all code blocks
- **Line highlighting** - Highlight specific lines
- **Annotations** - Add inline comments to code

### Configuration

```yaml
markdown_extensions:
  - pymdownx.highlight:
      anchor_linenums: true
      line_spans: __span
      pygments_lang_class: true
  - pymdownx.inlinehilite
  - pymdownx.snippets
  - pymdownx.superfences
```

### Usage

Specify the language after the opening fence:

````markdown
```python
def hello_world():
    print("Hello, World!")
```
````

### Line Highlighting

Highlight specific lines:

````markdown
```python hl_lines="2 3"
def hello_world():
    name = "World"  # Highlighted
    print(f"Hello, {name}!")  # Highlighted
```
````

### Line Numbers

Add line numbers:

````markdown
```python linenums="1"
def hello_world():
    print("Hello, World!")
```
````

## Additional Extensions

### Admonitions

Styled callout boxes for notes, warnings, tips:

```markdown
!!! note "This is a note"
    Important information here.

!!! warning "Be careful"
    Warning message here.

!!! tip "Pro tip"
    Helpful hint here.
```

### Task Lists

Interactive checkboxes:

```markdown
- [x] Completed task
- [ ] Pending task
- [ ] Another pending task
```

### Tables

Standard Markdown tables with Material styling:

```markdown
| Column 1 | Column 2 |
|----------|----------|
| Value 1  | Value 2  |
```

### Tabbed Content

Organize content in tabs:

```markdown
=== "Tab 1"
    Content for tab 1

=== "Tab 2"
    Content for tab 2
```

### Emoji Support

Use emoji shortcodes:

```markdown
:smile: :rocket: :heart:
```

Renders as: 😄 🚀 ❤️

## Theme Features

### Dark Mode

Automatic dark mode support:

- Follows system preference
- Manual toggle available
- Smooth transitions

### Navigation

- **Instant loading** - Fast page transitions
- **Progress indicator** - Loading bar for navigation
- **Breadcrumbs** - Clear page hierarchy
- **Table of contents** - Right sidebar on wide screens

### Responsive Design

- Mobile-optimized layout
- Touch-friendly navigation
- Adaptive typography

## Next Steps

- **Set up locally**: See [Setup Guide](setup.md)
- **Add content**: See [Content Guide](content-guide.md)
- **Build the site**: See [Building](building.md)
