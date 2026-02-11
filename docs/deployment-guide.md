# Deployment Guide

Complete guide for deploying applications generated from Trobz Python Template.

## Table of Contents

- [Using the Template](#using-the-template)
- [CLI Development](#cli-development)
- [Service Development](#service-development)
- [Service Deployment](#service-deployment)
- [Configuration for Deployment](#configuration-for-deployment)
- [GitHub Actions Configuration](#github-actions-configuration)
- [Troubleshooting](#troubleshooting)

## Using the Template

### Prerequisites

Before using the template, ensure you have:

- Python 3.12+ (for running the template generator)
- Copier 8.0+ installed (`pip install copier`)
- Git for version control
- uv package manager (automatically installed by generated Makefile)

### Generate a New Project

Use Copier to generate a project from the template:

```bash
# Generate from GitHub (recommended)
copier copy "https://github.com/trobz/trobz-python-template.git" ./my-project

# Or from local checkout
copier copy path/to/template ./my-project
```

### Interactive Configuration

Answer the prompts to customize your project:

```
project_name? [my-project]: My Application
package_name? [my_application]:
project_description? A brief description of your app
project_type? [cli]:
  1. cli
  2. service
  > 2
service_framework? [fastapi]:
  1. fastapi
  2. flask
  > 1
repository_namespace? [trobz]: myorg
repository_name? [my-application]:
author_username? [username]: john-doe
author_email? [john@example.com]:
enable_github_action? [False]: True
publish_to_pypi? [False]: True
```

### Initial Setup

After generation, initialize the project:

```bash
cd my-project

# Install dependencies and setup hooks
make install

# Verify installation
make check
make test
```

## CLI Development

### Project Structure

Generated CLI projects have this structure:

```
my-cli/
├── my_cli/
│   ├── __init__.py
│   └── main.py              # Typer CLI application
├── tests/
│   └── test_main.py
├── Makefile
├── pyproject.toml
├── ruff.toml
├── ty.toml
└── README.md
```

### Development Workflow

1. **Add Commands**: Extend `main.py` with new commands

```python
import typer

app = typer.Typer()

@app.command()
def hello(name: str = typer.Argument("World")):
    """Say hello to someone."""
    typer.echo(f"Hello {name}")

@app.command()
def goodbye(name: str):
    """Say goodbye to someone."""
    typer.echo(f"Goodbye {name}")
```

2. **Test Locally**: Run commands via uv

```bash
uv run my-cli hello Alice
uv run my-cli goodbye Bob
```

3. **Add Dependencies**

```bash
uv add requests
uv add --dev pytest-cov
```

4. **Build Distribution**

```bash
make build
# Creates dist/my_cli-*.whl
```

### CLI Deployment Options

#### Option 1: Install from Wheel

```bash
# Build the wheel
make build

# Install on target system
pip install dist/my_cli-1.0.0-py3-none-any.whl

# Use the CLI
my-cli hello World
```

#### Option 2: Install from Source

```bash
# On target system
git clone https://github.com/myorg/my-cli.git
cd my-cli
make install

# Use via uv
uv run my-cli hello World
```

#### Option 3: Publish to PyPI

```bash
# Configure PyPI credentials
# See "GitHub Actions Configuration" section

# Tag for release
git tag v1.0.0
git push origin v1.0.0

# GitHub Actions automatically publishes to PyPI

# Users install with pip
pip install my-cli
```

## Service Development

### Project Structure

Generated service projects include:

```
my-api/
├── my_api/
│   ├── __init__.py
│   ├── main.py              # FastAPI/Flask application
│   └── settings.py          # Pydantic settings
├── tests/
│   └── test_main.py
├── .env.sample              # Configuration template
├── Makefile
├── pyproject.toml
└── README.md
```

### Local Development

1. **Setup Environment**

```bash
make install

# Copy environment template
cp .env.sample .env.local

# Edit configuration
ENVIRONMENT=development
DEBUG=true
SERVER_HOST=0.0.0.0
SERVER_PORT=8000
```

2. **Run Development Server**

```bash
# FastAPI (with auto-reload)
make serve

# Or manually
uv run uvicorn my_api.main:app --reload

# Access at http://localhost:8000
```

3. **Add Routes** (FastAPI example)

```python
from fastapi import FastAPI
from my_api.settings import get_settings

app = FastAPI()
settings = get_settings()

@app.get("/")
async def root():
    return {"message": "Hello World", "env": settings.environment}

@app.get("/health")
async def health():
    return {"status": "ok"}

@app.get("/users/{user_id}")
async def get_user(user_id: int):
    return {"user_id": user_id, "name": "John Doe"}
```

4. **Test API**

```bash
# Health check
curl http://localhost:8000/health

# View docs
open http://localhost:8000/docs

# Run tests
make test
```

## Service Deployment

### Deployment Architecture

Choose deployment method based on requirements:

| Method | Use Case | Complexity | Scalability |
|--------|----------|-----------|-------------|
| Uvicorn (Direct) | Development, testing | Low | Low |
| Gunicorn + Uvicorn | Production, single server | Medium | Medium |
| Docker | Containerized deployment | Medium | High |
| Docker Compose | Multi-service apps | Medium | Medium |
| Kubernetes | Cloud-native, high scale | High | Very High |
| Platform Services | Quick deployment | Low | High |

### Method 1: Gunicorn + Uvicorn Workers

**Best for:** Production single-server deployments

```bash
# Install gunicorn
uv add gunicorn

# Run with workers
gunicorn my_api.main:app \
  --workers 4 \
  --worker-class uvicorn.workers.UvicornWorker \
  --bind 0.0.0.0:8000 \
  --access-logfile - \
  --error-logfile -
```

**Create systemd service** (`/etc/systemd/system/my-api.service`):

```ini
[Unit]
Description=My API Service
After=network.target

[Service]
Type=notify
User=www-data
Group=www-data
WorkingDirectory=/opt/my-api
Environment="PATH=/opt/my-api/.venv/bin"
ExecStart=/opt/my-api/.venv/bin/gunicorn my_api.main:app \
  --workers 4 \
  --worker-class uvicorn.workers.UvicornWorker \
  --bind 0.0.0.0:8000
Restart=always

[Install]
WantedBy=multi-user.target
```

**Enable and start**:

```bash
sudo systemctl daemon-reload
sudo systemctl enable my-api
sudo systemctl start my-api
sudo systemctl status my-api
```

## Configuration for Deployment

### Environment Files

Service projects use `.env` files for configuration:

```bash
# .env.local (not tracked, highest priority)
ENVIRONMENT=production
DEBUG=false
SERVER_HOST=0.0.0.0
SERVER_PORT=8000

# Database
DATABASE_URL=postgresql://user:pass@localhost:5432/mydb

# API Keys
API_SECRET_KEY=your-secret-key-here
EXTERNAL_API_KEY=external-service-key
```

### Configuration Precedence

Settings are loaded in this order (highest priority first):

1. `.env.local` (local overrides, not committed)
2. `.env` (tracked, can be committed for defaults)
3. Environment variables (system-level)
4. Defaults in `settings.py`

### Settings Management

**Example settings.py**:

```python
from pydantic_settings import BaseSettings, SettingsConfigDict

class Settings(BaseSettings):
    environment: str = "development"
    debug: bool = False
    server_host: str = "0.0.0.0"
    server_port: int = 8000

    # Database
    database_url: str = "sqlite:///./app.db"

    # Security
    api_secret_key: str = "dev-secret-key"

    model_config = SettingsConfigDict(
        env_file=('.env.local', '.env'),
        env_file_encoding='utf-8',
        extra='ignore'
    )

def get_settings() -> Settings:
    return Settings()
```

### Security Best Practices

**Never commit sensitive data**:

```gitignore
# .gitignore
.env.local
.env.production
*.key
*.pem
secrets/
```

**Use environment-specific files**:

```bash
# Development
.env.local
```

**Rotate secrets regularly**:

```bash
# Generate new secret key
python -c "import secrets; print(secrets.token_urlsafe(32))"
```

## GitHub Actions Configuration

### Enable CI/CD

When generating project, set `enable_github_action = true` to include workflows.

### Included Workflows

#### 1. test.yaml - Multi-version Testing

Runs on pull requests and main branch pushes:

```yaml
# .github/workflows/test.yaml
name: Test

on:
  pull_request:
  push:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        python-version: ['3.10', '3.11', '3.12', '3.13']
    steps:
      - uses: actions/checkout@v4
      - name: Setup Python Environment
        uses: ./.github/actions/setup-python-env
        with:
          python-version: ${{ matrix.python-version }}
      - name: Run tests
        run: make test
```

#### 2. pre-commit.yaml - Code Quality

Runs linting and formatting checks:

```yaml
# .github/workflows/pre-commit.yaml
name: Pre-commit

on:
  pull_request:
  push:
    branches: [main]

jobs:
  check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Setup Python Environment
        uses: ./.github/actions/setup-python-env
      - name: Run pre-commit
        run: make check
```

### PyPI Publishing Setup

**Prerequisites**:

1. Create PyPI account at https://pypi.org
2. Configure Trusted Publishing in PyPI project settings
3. Add repository secrets in GitHub

**Configure Trusted Publishing** (recommended):

1. Go to PyPI project settings
2. Add GitHub publisher
3. Enter: `repository_namespace/repository_name`
4. Workflow: `release.yaml`

**Or use API token** (legacy):

1. Generate PyPI API token
2. Add to GitHub secrets: `PYPI_TOKEN`

**Trigger release**:

```bash
# Commit with conventional format
git commit -m "feat: add new feature"
git push

# Semantic release creates tag automatically
# Or manually tag:
git tag v1.0.0
git push origin v1.0.0
```

### Custom GitHub Action

The template includes a reusable action for environment setup:

```yaml
# .github/actions/setup-python-env/action.yaml
name: Setup Python Environment
description: Setup Python with uv and install dependencies

inputs:
  python-version:
    description: Python version to use
    required: false
    default: '3.12'

runs:
  using: composite
  steps:
    - name: Set up Python
      uses: actions/setup-python@v5
      with:
        python-version: ${{ inputs.python-version }}

    - name: Install uv
      shell: bash
      run: pip install uv

    - name: Install dependencies
      shell: bash
      run: uv sync
```

## Troubleshooting

### Common Issues

#### Template Generation Fails

**Issue**: Copier command fails with "template not found"

**Solution**:
```bash
# Verify Copier installation
copier --version  # Should be 8.0+

# Use full HTTPS URL
copier copy "https://github.com/trobz/trobz-python-template.git" ./my-project

# Or clone first
git clone https://github.com/trobz/trobz-python-template.git
copier copy ./trobz-python-template ./my-project
```

#### uv Not Found

**Issue**: `make install` fails with "uv: command not found"

**Solution**:
```bash
# Install uv globally
pip install uv

# Or use with pipx
pipx install uv

# Verify installation
uv --version
```

#### Pre-commit Hooks Fail

**Issue**: Commits rejected by pre-commit hooks

**Solution**:
```bash
# Run checks manually
make check

# Fix formatting issues
uv run ruff format .

# Fix linting issues
uv run ruff check --fix .

# Update hooks
uv run pre-commit autoupdate
```

#### Tests Failing

**Issue**: `make test` fails with import errors

**Solution**:
```bash
# Reinstall dependencies
uv sync --refresh

# Clear cache
uv cache clean

# Run tests with verbose output
uv run pytest -v

# Check Python version
python --version  # Should be 3.10+
```

#### Service Won't Start

**Issue**: `make serve` fails with port already in use

**Solution**:
```bash
# Check what's using the port
lsof -i :8000

# Kill process
kill -9 <PID>

# Or use different port
SERVER_PORT=8001 make serve
```

#### GitHub Actions Failing

**Issue**: CI/CD workflows fail on GitHub

**Solution**:
```bash
# Check workflow syntax locally
# Install act: https://github.com/nektos/act
act -l

# Test workflow locally
act push

# Check secrets are set
gh secret list

# View workflow logs
gh run view --log
```

#### PyPI Publishing Fails

**Issue**: Release workflow fails to publish to PyPI

**Solution**:
```bash
# Verify PYPI_TOKEN secret exists
gh secret list | grep PYPI_TOKEN

# Test build locally
make build
ls dist/

# Verify token has correct permissions
# Go to PyPI → Account Settings → API tokens

# Check trusted publisher configuration
# PyPI project → Settings → Publishing
```

### Getting Help

If you encounter issues not covered here:

1. **Check Documentation**:
   - [README.md](../README.md)
   - [Code Standards](code-standards.md)
   - [Codebase Summary](codebase-summary.md)

2. **Search Issues**:
   - https://github.com/trobz/trobz-python-template/issues

3. **Ask for Help**:
   - GitHub Discussions: https://github.com/trobz/trobz-python-template/discussions
   - Create new issue: https://github.com/trobz/trobz-python-template/issues/new

4. **Community Resources**:
   - Copier docs: https://copier.readthedocs.io/
   - uv docs: https://docs.astral.sh/uv/
   - FastAPI docs: https://fastapi.tiangolo.com/
   - Typer docs: https://typer.tiangolo.com/
