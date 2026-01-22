# Building the Site

This guide explains how to build the static site locally for testing and debugging.

## Production Build

To build the production site locally:

```bash
uv run mkdocs build
```

This generates the static site in the `site/` directory.

### What Gets Generated

```text
site/
├── index.html          # Homepage
├── posts/
│   └── index.html
├── notes/
│   └── index.html
├── assets/
│   ├── stylesheets/   # Minified CSS
│   └── javascripts/   # Minified JS
└── search/
    └── search_index.json
```

### Testing the Build

You can serve the built site locally:

```bash
python -m http.server --directory site 8000
```

Then open `http://localhost:8000` in your browser.

## Strict Mode (Recommended)

Always build with strict mode before committing:

```bash
uv run mkdocs build --strict
```

Strict mode treats warnings as errors and catches issues like:

- Broken internal links
- Missing files referenced in navigation
- Invalid markdown syntax
- Duplicate headings
- Unreferenced files

### Example Strict Mode Error

```text
ERROR - Doc file 'notes/nonexistent.md' contains a link to
'../other/missing.md' which is not found in the docs files.
```

**Fix:** Remove the broken link or create the missing file.

## Common Build Issues

### Broken Links

**Error:**

```text
WARNING - Doc file 'notes/my-note.md' contains a link to
'other-note.md' which is not found in the docs files.
```

**Solution:**

- Check the link path is correct (relative to current file)
- Verify the target file exists
- Use `mkdocs serve` to see live link validation

### Missing Navigation Files

**Error:**

```text
WARNING - A reference to 'notes/deleted-file.md' is included in
the 'nav' configuration, which is not found in the documentation files.
```

**Solution:**

- Remove the file from `mkdocs.yml` navigation
- Or recreate the deleted file

### Git Revision Date Warnings

**Warning:**

```text
[git-revision-date-localized-plugin] '/path/to/file.md' has no git
logs, using current timestamp
```

**Cause:** File hasn't been committed to git yet

**Solution:**

- Commit the file, or
- Ignore the warning (it uses current date as fallback)

### Invalid YAML in mkdocs.yml

**Error:**

```text
Config file 'mkdocs.yml' is invalid
```

**Solution:**

- Check YAML syntax (indentation, colons, quotes)
- Use `yamllint mkdocs.yml` to find issues
- Validate YAML online: [YAML Lint](http://www.yamllint.com/)

## Cleaning Build Artifacts

To remove the generated `site/` directory:

```bash
rm -rf site/
```

MkDocs automatically cleans the directory on each build, so manual cleaning is rarely needed.

## Build Performance

**Typical build times:**

- Small site (< 50 pages): 1-3 seconds
- Medium site (50-200 pages): 3-10 seconds
- Large site (200+ pages): 10-30 seconds

**Factors affecting build time:**

- Number of pages
- Image optimization
- Search index generation
- Minification (HTML, CSS, JS)

## Deployment Build

For deployment, the CI/CD workflow runs:

```bash
uv run mkdocs build --strict
```

This ensures:

- All checks pass before deployment
- No warnings or errors in production
- Broken links are caught before going live

See [CI/CD Workflow](ci-cd.md) for deployment details.

## Next Steps

- **Deploy changes**: See [CI/CD Workflow](ci-cd.md)
- **Add content**: See [Content Guide](content-guide.md)
- **Fix linting**: See [Linting](linting.md)
