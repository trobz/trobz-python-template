# Codebase Summary

## Overview

Trobz Python Template is a Copier-based template generator for creating Python projects with modern tooling. It supports CLI applications (using Typer) and web services (using FastAPI or Flask), all managed with the uv package manager.

**Repository:** https://github.com/trobz/trobz-python-template

## Directory Structure

```
.
├── template/                          # Copier template source
│   ├── {{package_name}}/              # Package directory template
│   │   ├── __init__.py                # Package initialization
│   │   ├── main.py.jinja              # CLI or service entry point
│   │   └── settings.py.jinja          # Service settings (Pydantic)
│   ├── .github/                       # GitHub workflows (conditional)
│   │   ├── actions/setup-python-env/  # Custom action for env setup
│   │   └── workflows/
│   │       ├── test.yaml              # Multi-version test workflow
│   │       ├── pre-commit.yaml        # Code quality checks
│   │       └── release.yaml.jinja     # Semantic release workflow
│   ├── pyproject.toml.jinja           # Project configuration
│   ├── Makefile.jinja                 # Build and test targets
│   ├── ruff.toml                      # Ruff linter config
│   ├── ty.toml                        # Type checker config
│   ├── .pre-commit-config.yaml        # Pre-commit hooks
│   ├── .gitignore                     # Git ignore rules
│   ├── .env.sample                    # Environment template (service only)
│   └── README.md.jinja                # Generated project README
├── plans/                             # Development planning templates
├── copier.yaml                        # Copier configuration
├── pyproject.toml                     # Template generator project config
├── CHANGELOG.md                       # Version history
└── LICENSE                            # AGPL-3.0
```

## Key Components

### Copier Configuration (copier.yaml)

Defines interactive prompts for users:

- **project_name**: User-friendly project name
- **package_name**: Python package identifier (validated format)
- **project_type**: `cli` or `service`
- **service_framework**: `fastapi` or `flask` (conditional on service type)
- **repository_namespace**: GitHub namespace (default: trobz)
- **repository_name**: Repository identifier
- **author_username**: Author GitHub handle
- **author_email**: Author email
- **enable_github_action**: Toggle GitHub Actions workflows
- **publish_to_pypi**: Enable PyPI publishing (requires GitHub Actions)

### Template Variables

The Jinja2 templates use variables populated during generation:

- `{{ project_name }}`: Project display name
- `{{ package_name }}`: Python package name (snake_case)
- `{{ project_description }}`: Project description
- `{{ repository_namespace }}`: GitHub org/user
- `{{ repository_name }}`: Repository name
- `{{ author_username }}`: Author identifier
- `{{ author_email }}`: Author contact
- `{{ project_type }}`: `cli` or `service`
- `{{ service_framework }}`: `fastapi` or `flask`
- `{{ _copier_conf.answers_file }}`: Answers storage file

### Conditional Blocks

Templates use Jinja2 conditionals to customize output:

```jinja
{% if project_type == 'service' %}
  # Service-specific content
{% endif %}

{% if enable_github_action %}
  # Include GitHub workflows
{% endif %}

{% if service_framework == 'fastapi' %}
  # FastAPI-specific code
{% elif service_framework == 'flask' %}
  # Flask-specific code
{% endif %}
```

## Project Types

### CLI Application

- Framework: Typer (command-line toolkit)
- Entry point: Console script in pyproject.toml
- Use case: Command-line tools, automation scripts
- Generated structure: Simple package with hello command example

### Service Application

#### FastAPI Option
- Framework: FastAPI (modern async web framework)
- Server: Uvicorn (ASGI server)
- Features: Automatic OpenAPI docs, async support
- Config: Pydantic Settings with environment file support
- Health endpoint: `GET /health` for monitoring

#### Flask Option
- Framework: Flask (lightweight synchronous web framework)
- Server: Uvicorn (also supports standard WSGI)
- Features: Minimalist approach, easier learning curve
- Config: Pydantic Settings with environment file support
- Health endpoint: `GET /health` for monitoring

Both service options include:
- Settings management via `.env` and `.env.local` files
- Root endpoint: `GET /` returns hello message with environment
- Configurable host and port
- Debug mode toggle

## Tooling Configuration

### Code Quality (ruff)

Located at `template/ruff.toml`, configured with:
- Import sorting (isort compatibility)
- Formatting rules
- Linting rules (E, F, I, N, etc.)

### Type Checking (ty)

Located at `template/ty.toml`, provides static type verification.

### Testing (pytest)

Standard pytest configuration, run via `make test`.

### Pre-commit Hooks

```yaml
repos:
  - pre-commit/pre-commit-hooks: Basic checks (TOML, YAML, trailing whitespace)
  - astral-sh/ruff-pre-commit: Code formatting and linting
```

## Build System

### uv (Package Manager)

- Replaces pip/poetry with faster Rust-based manager
- Manages Python versions
- Lockfile: `uv.lock` (committed)
- Commands: `uv sync`, `uv add`, `uv run`

### Build Backend

- Uses `uv_build` backend in pyproject.toml
- Build command: `uvx --from build pyproject-build --installer uv`
- Output: Wheel file in dist/

## CI/CD Workflows

### test.yaml

Runs on PR and main push:
- Matrix: Python 3.10, 3.11, 3.12, 3.13
- Command: `make test`
- Allows failures on non-required versions

### pre-commit.yaml

Runs on PR and main push:
- Command: `make check`
- Ensures code quality before merge

## Version Management

### Semantic Release

- Tool: python-semantic-release
- Triggers: Automatic on commit messages (conventional format)
- Versions: Respects semantic versioning (major.minor.patch)
- Changelog: Auto-generated from commits

### Conventional Commits

Required format for automation:
- `feat:` → minor version bump
- `fix:` → patch version bump
- `BREAKING CHANGE:` → major version bump
- Excluded patterns: `chore:`, `ci:`, `style:`, `test:`, `build:`

## Generated Project Structure

After running copier, users get:

```
my-project/
├── my_package/
│   ├── __init__.py
│   ├── main.py              # CLI or service entry point
│   └── settings.py          # Service settings (if service)
├── tests/
│   └── test_main.py
├── .github/workflows/       # CI/CD (if enabled)
├── .env.sample              # (if service)
├── Makefile
├── pyproject.toml
├── .pre-commit-config.yaml
├── ruff.toml
├── ty.toml
├── .gitignore
└── README.md
```

## Key Dependencies

### Template Generator (requires Python 3.12+)
- copier: Template scaffolding
- ruff: Linting and formatting
- pre-commit: Git hooks

### Generated Projects (support Python 3.10+)
- CLI: typer
- Service: fastapi or flask
- Service: uvicorn (ASGI server)
- Config: pydantic-settings
- Type checking: ty (optional)

## Configuration Files

### pyproject.toml

Defines project metadata and tools:
- Poetry dependencies
- semantic-release config
- Tool settings (ruff, pytest, etc.)

### .gitignore

Comprehensive ignore rules for:
- Python artifacts (__pycache__, *.pyc, *.egg-info)
- Virtual environments
- IDE files (.vscode, .idea)
- Build outputs (dist/, build/)

### Makefile

Provides convenient commands:
- `make install`: Setup environment
- `make check`: Run code quality checks
- `make test`: Run tests with pytest
- `make build`: Create wheel
- `make serve`: Run service (service projects only)

## Customization Points

Users can customize:
1. **Initial Prompts** (copier.yaml): Define variables for their projects
2. **Templates** (*.jinja files): Modify generated code structure
3. **Workflows**: Adjust GitHub Actions behavior
4. **Dependencies**: Add more packages via pyproject.toml
5. **Tool Config**: Modify ruff, pytest, etc. rules

## Security Considerations

- **License**: AGPL-3.0 (open-source, viral copyleft)
- **Secret Management**: `.env` files for sensitive config (not committed)
- **Pre-commit Hooks**: Prevents accidental secrets in commits
- **GitHub Secrets**: Required for PyPI token if publishing

## Design Patterns

- **Single Responsibility**: Each file has focused purpose
- **Configuration as Code**: All settings in pyproject.toml
- **Infrastructure as Code**: Workflows defined in YAML
- **Convention Over Configuration**: Sensible defaults reduce setup
- **Conditional Inclusion**: Jinja2 enables flexible scaffolding
