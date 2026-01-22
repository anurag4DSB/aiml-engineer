# CI/CD Workflow

The project uses GitHub Actions to automatically build and deploy the site when changes are pushed to the `main` branch.

## Workflow Overview

**Workflow file:** `.github/workflows/publish-docs.yml`

**Trigger:** Every push to `main` branch

**Outcome:** Site deployed to `https://aiml.engineer`

## Workflow Steps

### 1. Checkout Repository

```yaml
uses: actions/checkout@v4
with:
  fetch-depth: 0
```

Clones the repository with full git history. Full history is needed for the git-revision-date plugin to show accurate "last updated" dates on pages.

### 2. Install uv

```yaml
uses: astral-sh/setup-uv@v4
```

Sets up the uv package manager. This action caches uv downloads for faster subsequent runs.

### 3. Install Python

```bash
uv python install 3.13
```

Installs Python 3.13 using uv's built-in Python version management.

### 4. Install Dependencies

```bash
uv sync --frozen --group dev
```

Syncs all dependencies from lockfile:

- `--frozen` - Fails if lockfile is outdated (prevents version drift)
- `--group dev` - Includes dev dependencies (linting tools) plus main dependencies

### 5. Lint Markdown Files

```bash
uv run pymarkdown scan -r content/ README.md CONTRIBUTING.md NOTICE.md
```

Validates all markdown files against configured rules. See [Linting](linting.md) for details.

### 6. Lint YAML Files

```bash
uv run yamllint .
```

Validates all YAML files (workflow files, mkdocs.yml, etc.) against configured rules.

### 7. Build Site

```bash
uv run mkdocs build --strict
```

Builds the MkDocs site with strict mode enabled. Fails on warnings to catch errors early.

### 8. Deploy to GitHub Pages

```yaml
uses: peaceiris/actions-gh-pages@v4
with:
  publish_dir: site
  cname: aiml.engineer
  github_token: ${{ secrets.GITHUB_TOKEN }}
```

Deploys the `site/` directory to the `gh-pages` branch and maintains the `CNAME` file for the custom domain.

## Typical Workflow Duration

- **First run (cold cache):** 2-3 minutes
- **Subsequent runs (cached):** 30-60 seconds

The uv setup action caches downloads, making subsequent runs much faster.

## Monitoring Deployments

### View Workflow Runs

1. Go to repository on GitHub
2. Click "Actions" tab
3. View status of recent deployments

### Workflow Status Indicators

- ✅ **Green checkmark** - Deployment successful
- ❌ **Red X** - Deployment failed
- 🟡 **Yellow dot** - Deployment in progress
- ⚪ **Gray** - Workflow skipped or cancelled

## Troubleshooting Failed Builds

### Step 1: Identify the Failed Step

Click on the failed workflow run and expand the step that failed:

- "Lint markdown files" - Markdown formatting issues
- "Lint YAML files" - YAML syntax issues
- "Build site" - MkDocs build errors
- "Deploy" - GitHub Pages deployment issues

### Step 2: Read the Error Message

Common errors and solutions:

#### Linting Failures

**Error:**

```text
./content/notes/my-note.md:23:1: MD040: Fenced code blocks
should have a language specified
```

**Solution:** Add language to code block:

````markdown
```python
# your code here
```
````

#### Build Failures

**Error:**

```text
ERROR - Doc file 'notes/example.md' contains a link to
'missing.md' which is not found.
```

**Solution:** Fix or remove the broken link.

**Error:**

```text
ERROR - Config file 'mkdocs.yml' is invalid
```

**Solution:** Check YAML syntax in mkdocs.yml.

#### Dependency Issues

**Error:**

```text
error: The lockfile at `uv.lock` needs to be updated
```

**Solution:** Update lockfile locally:

```bash
uv sync
git add uv.lock
git commit -m "Update lockfile"
git push
```

### Step 3: Test Locally

Always test locally before pushing:

```bash
# Run linting
uv run pre-commit run --all-files

# Build with strict mode
uv run mkdocs build --strict

# Serve locally
uv run mkdocs serve
```

## Manual Deployment

If you need to trigger a deployment manually:

```bash
# From main branch
git commit --allow-empty -m "Trigger deployment"
git push origin main
```

This creates an empty commit that triggers the workflow.

## Deployment Environments

**Staging:** None (all changes go directly to production)

**Production:** `https://aiml.engineer` (deployed from `main` branch)

**Note:** Always test changes locally with `mkdocs serve` before pushing to main.

## Workflow Permissions

The workflow requires these permissions:

```yaml
permissions:
  contents: write  # Push to gh-pages branch
  pages: write     # Deploy to GitHub Pages
  id-token: write  # OIDC token for deployment
```

These are set in the workflow file and don't require manual configuration.

## Next Steps

- **Fix build errors**: See [Building](building.md)
- **Fix linting errors**: See [Linting](linting.md)
- **Add content**: See [Content Guide](content-guide.md)
