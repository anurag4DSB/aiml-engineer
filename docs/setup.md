# Setup Guide

This guide will help you set up your local development environment for contributing to aiml.engineer.

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

## Tips and Troubleshooting

### Development Server Tips

- The server watches for changes and rebuilds automatically
- Press `Ctrl+C` to stop the server
- Use `uv run mkdocs serve --strict` to catch warnings during development

### Common Issues

**uv not found after installation:**
- Close and reopen your terminal
- Check that uv is in your PATH: `which uv` (macOS/Linux) or `where uv` (Windows)

**Port 8000 already in use:**
- Use a different port: `uv run mkdocs serve --dev-addr 127.0.0.1:8001`
- Or stop the process using port 8000

**Git history warnings:**
- Make sure you cloned with full history (not shallow clone)
- The git-revision-date plugin needs commit history for "last updated" dates

## Next Steps

- **Add content**: See [Content Guide](content-guide.md)
- **Set up linting**: See [Linting](linting.md)
- **Understand the structure**: See [Project Structure](project-structure.md)
