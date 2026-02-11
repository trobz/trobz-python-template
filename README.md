# Trobz Python Template

A modern [Copier](https://copier.readthedocs.io/) template for Python projects managed by [uv](https://docs.astral.sh/uv/). Scaffold production-ready CLI applications and web services with zero boilerplate, modern tooling, and best practices built-in.

**Current Version:** 1.1.0

## Features

- **Project Type Support**
  - CLI applications with [Typer](https://typer.tiangolo.com/)
  - Web services with [FastAPI](https://fastapi.tiangolo.com/) or [Flask](https://flask.palletsprojects.com/)

- **Modern Python Tooling**
  - [uv](https://docs.astral.sh/uv/) for fast dependency management
  - [ruff](https://docs.astral.sh/ruff/) for linting and formatting
  - [pytest](https://docs.pytest.org/) for testing
  - [ty](https://github.com/asottile/py) for static type checking
  - [pre-commit](https://pre-commit.com/) for code quality gates

- **GitHub Integration**
  - Automated testing on multiple Python versions (3.10, 3.11, 3.12, 3.13)
  - Pre-commit checks on every push
  - Semantic versioning with [conventional commits](https://www.conventionalcommits.org/)
  - Optional PyPI publishing with Trusted Publishing
  - Custom GitHub Action for consistent environment setup

- **Developer Experience**
  - Interactive prompts with input validation
  - Makefile with helpful commands
  - Pre-configured code quality tools
  - Health endpoints for services
  - Environment-based configuration

- **Production-Ready**
  - AGPL-3.0 license for open-source compliance
  - Type hints throughout codebase
  - Comprehensive error handling
  - Security best practices built-in

## Quick Start

### Prerequisites

- Python 3.12+ (for running the template generator)
- Copier (install via `pip install copier`)
- Git (for version control)

### Generate a New Project

```bash
# Create a new CLI application
copier copy "https://github.com/trobz/trobz-python-template.git" ./my-cli-app

# Or create a new FastAPI service
copier copy "https://github.com/trobz/trobz-python-template.git" ./my-api-service
```

### Follow Interactive Prompts

```
project_name? [my-project]: My Awesome App
package_name? [my_awesome_app]:
project_description? Your app description
project_type? [cli]:
  1. cli
  2. service
  > 1
repository_namespace? [trobz]: myorg
repository_name? [my-awesome-app]:
author_username? [my-username]: jane-doe
author_email? [jane@example.com]:
enable_github_action? [False]: True
```

### Start Developing

```bash
cd my-awesome-app

# Install dependencies and pre-commit hooks
make install

# Run code quality checks
make check

# Run tests
make test

# For services: run the application
make serve
```

## Project Type Details

### CLI Application

Generate a command-line tool using Typer:

```bash
copier copy "https://github.com/trobz/trobz-python-template.git" ./my-cli
# Select: project_type = cli
```

**Generated structure:**
```
my-cli/
├── my_cli/
│   ├── __init__.py
│   └── main.py              # Typer CLI app
├── tests/
├── Makefile
└── pyproject.toml
```

**Example usage:**
```bash
$ make install
$ uv run my-cli hello World
Hello World
```

**Customize by:**
- Adding more commands with `@app.command()` decorators
- Adding options with `typer.Option()`
- Building sophisticated CLI hierarchies

### Service Application (FastAPI)

Generate a modern async web service:

```bash
copier copy "https://github.com/trobz/trobz-python-template.git" ./my-api
# Select: project_type = service, service_framework = fastapi
```

**Generated structure:**
```
my-api/
├── my_api/
│   ├── __init__.py
│   ├── main.py              # FastAPI app
│   └── settings.py          # Pydantic settings
├── .env.sample              # Config template
├── tests/
├── Makefile
└── pyproject.toml
```

**Example usage:**
```bash
$ make install

# Start the server
$ make serve
INFO:     Uvicorn running on http://0.0.0.0:8000
INFO:     Application startup complete

# Test endpoints
$ curl http://localhost:8000/
{"Hello":"World","env":"local"}

$ curl http://localhost:8000/health
{"status":"ok"}

# View API documentation
$ open http://localhost:8000/docs
```

**Key features:**
- Automatic OpenAPI/Swagger docs at `/docs`
- Async route support for high concurrency
- Pydantic request/response validation
- Uvicorn ASGI server included

### Service Application (Flask)

Generate a lightweight web service:

```bash
copier copy "https://github.com/trobz/trobz-python-template.git" ./my-service
# Select: project_type = service, service_framework = flask
```

**Similar structure to FastAPI but with:**
- Synchronous route handling
- Familiar Flask patterns
- Smaller dependency footprint
- WSGI-compatible

## Configuration and Customization

### Environment Variables

Service projects use `.env` files for configuration:

```bash
# Copy template to actual config
cp .env.sample .env.local

# Edit with your settings
ENVIRONMENT=development
DEBUG=true
SERVER_PORT=8001
```

**Configuration precedence:**
1. `.env.local` (highest priority, not tracked)
2. `.env` (tracked, can be committed)
3. Environment variables (system-level)
4. Defaults in Settings class

### Making Project Decisions

Edit generated files to customize:

```python
# Add dependencies: uv add requests
# Add new routes (services): edit my_package/main.py
# Add new commands (CLI): edit my_package/main.py
# Modify settings (services): edit my_package/settings.py
# Update workflows: edit .github/workflows/
```

## Development Workflow

### Available Commands

All projects include a Makefile with common tasks:

```bash
make install        # Install dependencies and pre-commit hooks
make check          # Lint, format, and type-check code
make test           # Run pytest with coverage
make build          # Build distribution wheel
make help           # Show all available commands

# Services only:
make serve          # Run the FastAPI/Flask application
```

### Code Quality Gates

Before committing, code must pass:

```bash
# Local checks (pre-commit hooks)
make check          # Ruff formatting/linting, pre-commit

# Commit must follow conventional format
# Example: feat(auth): add JWT support

# CI checks (GitHub Actions)
make test           # Tests on Python 3.10-3.13
```

### Version Management

Versions bump automatically based on commits:

```bash
# Regular feature
git commit -m "feat(api): add user endpoint"  # → 1.1.0

# Bug fix
git commit -m "fix(auth): resolve token bug"  # → 1.0.1

# Breaking change
git commit -m "refactor!: change API response format"  # → 2.0.0
```

## GitHub Actions Integration (Optional)

Enable during template generation with `enable_github_action = true`:

**Included workflows:**

| Workflow | Trigger | Purpose |
|---|---|---|
| test.yaml | PR & main push | Run tests on Python 3.10-3.13 |
| pre-commit.yaml | PR & main push | Linting and formatting checks |
| release.yaml | Tags | Semantic versioning and PyPI publish |

**PyPI Publishing (Optional)**

Enable with `publish_to_pypi = true` to automatically publish:
1. Create GitHub repo secrets: `PYPI_TOKEN`
2. Set token to PyPI Trusted Publishing
3. On version tag, automatically published to PyPI

## Documentation

Comprehensive documentation available:

- **[Project Overview & Requirements](./docs/project-overview-pdr.md)** - Vision, goals, and roadmap
- **[Code Standards](./docs/code-standards.md)** - Coding conventions and quality requirements
- **[System Architecture](./docs/system-architecture.md)** - Template design and data flows
- **[Codebase Summary](./docs/codebase-summary.md)** - Directory structure and components

## Requirements

### For Template Generator

- Python 3.12+
- Copier 8.0+
- Git

### For Generated Projects

- Python 3.10+ (template supports 3.10, 3.11, 3.12, 3.13)
- uv (automatically used by Makefile)
- Standard Unix tools (make, git)

## License

This template is licensed under the **AGPL-3.0 License**. Generated projects inherit this license.

## Contributing

Contributions are welcome! Please:

1. Read the [Code Standards](./docs/code-standards.md)
2. Follow conventional commit format
3. Ensure tests pass: `make test`
4. Ensure code passes checks: `make check`
5. Create a pull request with a clear description

## Support

- **Issues**: Report bugs and request features via [GitHub Issues](https://github.com/trobz/trobz-python-template/issues)
- **Discussions**: Ask questions in [GitHub Discussions](https://github.com/trobz/trobz-python-template/discussions)
- **Documentation**: See the [docs](./docs/) directory

## Changelog

See [CHANGELOG.md](./CHANGELOG.md) for detailed version history and changes.

## Related Projects

- [Copier](https://copier.readthedocs.io/) - Template engine
- [uv](https://docs.astral.sh/uv/) - Python package manager
- [Typer](https://typer.tiangolo.com/) - CLI framework
- [FastAPI](https://fastapi.tiangolo.com/) - Web framework
- [Ruff](https://docs.astral.sh/ruff/) - Linter and formatter
