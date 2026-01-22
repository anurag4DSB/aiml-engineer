# Code Quality and Linting

This project uses automated linting to maintain code quality. All markdown and YAML files are checked before commits and in CI.

## Install Pre-commit Hooks

After cloning the repository, install the pre-commit hooks:

```bash
uv sync --group dev
uv run pre-commit install
```

This will automatically run linting checks before each commit.

## Manual Linting

To manually run linting checks:

```bash
# Lint all markdown files
uv run pymarkdown scan -r content/ README.md CONTRIBUTING.md NOTICE.md

# Lint all YAML files
uv run yamllint .

# Run all pre-commit hooks manually
uv run pre-commit run --all-files
```

## Linting Rules

### Markdown

**Linter:** [pymarkdown](https://github.com/jackdewinter/pymarkdown)

**Configuration:** `pyproject.toml` under `[tool.pymarkdown]`

**Enabled rules:**

- All code blocks must have language specified
- No trailing whitespace
- Consistent heading styles
- Files must end with a newline

**Disabled rules:**

- **MD013** (line length) - Not enforced for documentation flexibility
- **MD041** (first line heading) - MkDocs uses frontmatter and various page structures
- **MD033** (inline HTML) - Material theme uses HTML for advanced features
- **MD022** (heading spacing) - Flexible spacing is more readable

### YAML

**Linter:** [yamllint](https://yamllint.readthedocs.io/)

**Configuration:** `.yamllint.yml`

**Rules:**

- Max line length: 120 characters
- Consistent indentation (2 spaces)
- Valid YAML syntax
- Comments must have space from content

**Ignored paths:**

- `.venv/` - Virtual environment (contains third-party YAML files)
- `site/` - Generated build output
- `uv.lock` - Generated lockfile

## Configuration Files

### Pre-commit Configuration

**File:** `.pre-commit-config.yaml`

Defines which hooks run before each commit:

- **trailing-whitespace** - Remove trailing spaces
- **end-of-file-fixer** - Ensure files end with newline
- **check-yaml** - Validate YAML syntax
- **check-added-large-files** - Prevent large files
- **check-merge-conflict** - Detect merge markers
- **pymarkdown** - Markdown linting
- **yamllint** - YAML linting

### PyMarkdown Configuration

**File:** `pyproject.toml` section `[tool.pymarkdown]`

Disables rules that conflict with documentation best practices.

### YAMLlint Configuration

**File:** `.yamllint.yml`

Sets line length, indentation rules, and ignore patterns.

## Bypassing Hooks (Emergency Only)

If you absolutely must commit without running hooks (not recommended):

```bash
git commit --no-verify -m "Your message"
```

**Use cases for bypass:**

- Emergency hotfix for broken production site
- Committing work-in-progress that will be fixed in next commit
- Linting rules are incorrectly flagging valid code (fix the rules instead if possible)

**Do not use for:**

- "I don't want to fix the linting errors" - Fix them instead
- "The checks are slow" - They're fast, this is not a valid reason
- "I'll fix it later" - Fix it now while the context is fresh

## Troubleshooting

### Pre-commit hooks not running

**Solution:**

```bash
# Reinstall hooks
uv run pre-commit uninstall
uv run pre-commit install

# Verify installation
uv run pre-commit run --all-files
```

### Linting fails on valid code

**Solution:**

1. Check if the rule makes sense for documentation
2. If not, add rule to disabled list in configuration
3. Document why the rule is disabled
4. Submit PR with the configuration change

### Merge conflicts in uv.lock

**Solution:**

```bash
# Regenerate lockfile
rm uv.lock
uv sync
uv sync --group dev
```

## Next Steps

- **Set up your environment**: See [Setup Guide](setup.md)
- **Add content**: See [Content Guide](content-guide.md)
- **Understand CI checks**: See [CI/CD Workflow](ci-cd.md)
