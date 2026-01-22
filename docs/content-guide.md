# Content Guide

This guide explains how to add and organize content in the aiml.engineer knowledge base.

## Content Organization

The site content lives in the `content/` directory with the following structure:

```text
content/
├── index.md            # Homepage
├── posts/              # Blog posts
├── notes/              # Technical notes
├── labs/               # Experiments and notebooks
├── projects/           # Project case studies
└── reading/            # Paper summaries
```

Each section follows this pattern:

- `index.md` - Section landing page with overview
- Individual content files in the same directory

## File Naming Conventions

### Posts

Use date-prefixed format:

```text
YYYY-MM-DD-title.md
```

**Examples:**

- `2025-09-26-hello-world.md`
- `2025-12-15-building-rag-system.md`

### Notes, Labs, Projects, Reading

Use descriptive kebab-case names:

```text
descriptive-name.md
```

**Examples:**

- `transformer-architecture.md`
- `fine-tuning-llama3.md`
- `rag-evaluation-metrics.md`

## Adding New Content

### 1. Create a New File

```bash
# Example: Adding a new note
touch content/notes/my-new-note.md
```

### 2. Add Content

Create your markdown content with a clear heading:

```markdown
# My New Note Title

Brief description of what this note covers.

## Section 1

Your content here...

## Section 2

More content...
```

### 3. Update Navigation (Optional)

By default, pages are discoverable through section index pages. To add a page to the main navigation, edit `mkdocs.yml`:

```yaml
nav:
  - Notes:
    - notes/index.md
    - My New Note: notes/my-new-note.md
```

### 4. Preview Locally

Always preview your changes before committing:

```bash
uv run mkdocs serve
```

Navigate to `http://127.0.0.1:8000` and verify:

- Content renders correctly
- Links work
- Code blocks have proper syntax highlighting
- Images load (if any)

## Markdown Features

### Code Blocks

Always specify the language for syntax highlighting:

````markdown
```python
def hello_world():
    print("Hello, World!")
```
````

### Admonitions

Material theme supports styled callouts:

```markdown
!!! note "This is a note"
    This is the content of the note.

!!! warning "Be careful"
    This is a warning message.

!!! tip "Pro tip"
    This is a helpful tip.
```

### Links

**Internal links (to other pages):**

```markdown
See the [Setup Guide](../../docs/setup.md) for installation instructions.
```

**External links:**

```markdown
Learn more at [MkDocs](https://www.mkdocs.org/).
```

### Images

```markdown
![Alt text](../images/my-image.png)
```

## Updating Dependencies

If you need to add or update a Python package:

### 1. Edit pyproject.toml

Add the dependency:

```toml
dependencies = [
    "mkdocs>=1.6.1",
    "mkdocs-material>=9.7.1",
    "your-new-package>=1.0.0",
]
```

### 2. Sync Dependencies

```bash
uv sync
```

This updates `uv.lock` with the new dependency graph.

### 3. Commit Both Files

**Important:** Always commit `uv.lock` alongside `pyproject.toml`:

```bash
git add pyproject.toml uv.lock
git commit -m "Add your-new-package dependency"
```

The lockfile ensures reproducible builds - everyone gets the exact same versions.

## Best Practices

### Writing Style

- Use clear, descriptive headings
- Keep paragraphs focused (3-5 sentences)
- Use bullet points for lists
- Add code examples where helpful
- Link to external resources

### Organization

- One topic per file
- Group related content in sections
- Update section index pages when adding content
- Use consistent naming conventions

### Code Examples

- Always specify language for code blocks
- Keep examples concise and focused
- Include comments for complex code
- Test code examples before publishing

### Links

- Prefer relative links for internal pages
- Use descriptive link text (not "click here")
- Check that links work before committing

## Next Steps

- **Build the site**: See [Building](building.md)
- **Set up linting**: See [Linting](linting.md)
- **Understand features**: See [Features](features.md)
