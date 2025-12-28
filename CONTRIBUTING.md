# Contributing to aiml.engineer

Thank you for your interest in contributing to this knowledge base. This guide will help you set up your local environment and understand the development workflow.

## Prerequisites

### Install uv

uv is an extremely fast Python package and project manager written in Rust. Install it using one of these methods:

**macOS/Linux:**

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

**Windows (PowerShell):**

```powershell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

**Using pip (alternative):**

```bash
pip install uv
```

**Verify installation:**

```bash
uv --version
```

For more installation options, see the [official uv documentation](https://docs.astral.sh/uv/getting-started/installation/).

### System Requirements

- Python 3.13 or higher (uv will install this automatically if needed)
- Git (for cloning the repository and revision date plugin)
- Text editor or IDE of your choice

## Local Setup

### 1. Clone the Repository

```bash
git clone https://github.com/anurag4dsb/aiml-engineer.git
cd aiml-engineer
```

### 2. Install Dependencies

The project uses a `pyproject.toml` file to define all dependencies. uv will automatically create a virtual environment and install everything:

```bash
uv sync
```

This command:

- Creates a `.venv` directory (virtual environment) if it doesn't exist
- Installs Python 3.13 if not already available
- Installs MkDocs, Material theme, and all required plugins
- Generates/updates `uv.lock` for reproducible builds

### 3. Verify Installation

Check that MkDocs is installed correctly:

```bash
uv run mkdocs --version
```

You should see output like:

```text
mkdocs, version 1.6.1 from /path/to/.venv/lib/python3.13/site-packages/mkdocs (Python 3.13)
```

### 4. Run Local Development Server

Start the MkDocs development server:

```bash
uv run mkdocs serve
```

This will:

- Build the site
- Start a local server at `http://127.0.0.1:8000`
- Watch for file changes and auto-reload the browser

Open your browser and navigate to `http://127.0.0.1:8000` to see the site locally.

**Tips:**

- The server watches for changes and rebuilds automatically
- Press `Ctrl+C` to stop the server
- Use `uv run mkdocs serve --strict` to catch warnings during development

## Code Quality and Linting

This project uses automated linting to maintain code quality. All markdown and YAML files are checked before commits and in CI.

### Install Pre-commit Hooks

After cloning the repository, install the pre-commit hooks:

```bash
uv sync --group dev
uv run pre-commit install
```

This will automatically run linting checks before each commit.

### Manual Linting

To manually run linting checks:

```bash
# Lint all markdown files
uv run pymarkdown scan -r docs/ README.md CONTRIBUTING.md NOTICE.md

# Lint all YAML files
uv run yamllint .

# Run all pre-commit hooks manually
uv run pre-commit run --all-files
```

### Linting Rules

**Markdown** (configured in `.pymarkdown`):

- All code blocks must have language specified
- No trailing whitespace
- Consistent heading styles
- Line length not enforced (flexible for documentation)

**YAML** (configured in `.yamllint.yml`):

- Max line length: 120 characters
- Consistent indentation (2 spaces)
- Valid YAML syntax

### Bypassing Hooks (Emergency Only)

If you need to commit without running hooks (not recommended):

```bash
git commit --no-verify -m "Your message"
```

## Project Structure

```text
aiml-engineer/
├── docs/                    # All documentation content
│   ├── index.md            # Homepage
│   ├── posts/              # Blog posts
│   ├── notes/              # Technical notes
│   ├── labs/               # Experiments and notebooks
│   ├── projects/           # Project case studies
│   └── reading/            # Paper summaries
├── mkdocs.yml              # MkDocs configuration
├── pyproject.toml          # Python project & dependencies
├── uv.lock                 # Locked dependency versions
└── .github/
    └── workflows/
        └── publish-docs.yml  # CI/CD workflow
```

### Content Organization

Each section (posts, notes, labs, etc.) follows this structure:

- `index.md` - Section landing page with overview
- Individual content files in the same directory

**File naming conventions:**

- Posts: `YYYY-MM-DD-title.md` (e.g., `2025-09-26-hello-world.md`)
- Notes/Labs/Projects: `descriptive-name.md` (e.g., `hello-note.md`)

## Making Changes

### Adding New Content

1. **Create a new file** in the appropriate section:

   ```bash
   # Example: Adding a new note
   touch docs/notes/my-new-note.md
   ```

2. **Add frontmatter and content:**

   ```markdown
   # My New Note Title

   Brief description of what this note covers.

   ## Section 1

   Your content here...
   ```

3. **Update navigation** (optional):
   If you want the page in the main navigation, edit `mkdocs.yml`:

   ```yaml
   nav:
     - Notes:
       - notes/index.md
       - My New Note: notes/my-new-note.md
   ```

4. **Preview locally:**

   ```bash
   uv run mkdocs serve
   ```

### Updating Dependencies

If you need to add or update a Python package:

1. **Edit `pyproject.toml`** and add the dependency:

   ```toml
   dependencies = [
       "mkdocs>=1.6.1",
       "mkdocs-material>=9.5.46",
       "your-new-package>=1.0.0",
   ]
   ```

2. **Sync dependencies:**

   ```bash
   uv sync
   ```

3. **Commit both files:**

   ```bash
   git add pyproject.toml uv.lock
   git commit -m "Add your-new-package dependency"
   ```

**Important:** Always commit `uv.lock` alongside `pyproject.toml` to ensure reproducible builds.

### Building the Site

To build the production site locally:

```bash
uv run mkdocs build
```

This generates the static site in the `site/` directory. You can:

- Inspect the generated HTML
- Test with a local HTTP server: `python -m http.server --directory site`

**Strict mode (recommended for testing):**

```bash
uv run mkdocs build --strict
```

This treats warnings as errors and catches issues like:

- Broken internal links
- Missing files referenced in navigation
- Invalid markdown syntax

## CI/CD Workflow

### How It Works

The project uses GitHub Actions to automatically build and deploy the site when changes are pushed to the `main` branch.

**Workflow file:** `.github/workflows/publish-docs.yml`

### Workflow Steps

1. **Trigger:** Runs on every push to `main` branch

2. **Checkout:** Clones the repository with full git history
   - Full history is needed for the git-revision-date plugin to show accurate "last updated" dates

3. **Install uv:** Sets up the uv package manager
   - Uses `astral-sh/setup-uv@v4` action
   - Caches uv downloads for faster subsequent runs

4. **Install Python:** Installs Python 3.13 using uv
   - `uv python install 3.13`

5. **Install dependencies:** Syncs all dependencies from lockfile
   - `uv sync --frozen` ensures exact versions from `uv.lock` are used
   - Fails if lockfile is outdated (prevents version drift)

6. **Build site:** Builds the MkDocs site with strict mode
   - `uv run mkdocs build --strict`
   - Fails on warnings to catch errors early

7. **Deploy:** Publishes to GitHub Pages
   - Uses `peaceiris/actions-gh-pages@v4`
   - Deploys `site/` directory to `gh-pages` branch
   - Maintains `CNAME` file for custom domain (aiml.engineer)

### Typical Workflow Duration

- First run (cold cache): ~2-3 minutes
- Subsequent runs (cached): ~30-60 seconds

### Monitoring Deployments

1. **View workflow runs:**
   - Go to repository on GitHub
   - Click "Actions" tab
   - View status of recent deployments

2. **Troubleshooting failed builds:**
   - Click on the failed workflow run
   - Expand the step that failed (usually "Build site" or "Install dependencies")
   - Review error messages
   - Common issues:
     - Broken internal links (use `mkdocs serve` locally to find)
     - Missing files in navigation
     - Outdated `uv.lock` (run `uv sync` locally)

### Manual Deployment

If you need to trigger a deployment manually:

```bash
# From main branch
git commit --allow-empty -m "Trigger deployment"
git push origin main
```

## Plugins and Features

### Git Revision Date

Shows "Last updated" and "Created" dates on each page automatically.

- Dates are extracted from git commit history
- Format: "2 days ago" (human-readable)
- Falls back to build date if git history unavailable

### Search

Built-in search functionality:

- Live suggestions as you type
- Highlights search terms on the page
- Shareable search URLs

### Minification

Automatically minifies HTML, CSS, and JavaScript:

- Reduces file sizes by ~30-50%
- Improves page load times
- Strips comments and unnecessary whitespace

### Code Highlighting

Syntax highlighting for code blocks:

- Supports 100+ programming languages
- Line numbers
- Copy button on all code blocks

## Getting Help

- **Documentation issues:** Open an issue in this repository
- **MkDocs questions:** See [MkDocs documentation](https://www.mkdocs.org/)
- **Material theme:** See [Material for MkDocs documentation](https://squidfunk.github.io/mkdocs-material/)
- **uv questions:** See [uv documentation](https://docs.astral.sh/uv/)

## License

Content in this repository is licensed under [Creative Commons Attribution 4.0 International (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/).

When contributing, you agree to license your contributions under the same terms.
