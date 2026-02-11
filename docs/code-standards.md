# Code Standards and Guidelines

## Overview

This document defines the code quality standards, tooling configuration, and conventions used in the Trobz Python Template and all generated projects. All code must follow these standards before being committed.

## Code Organization

### Project Structure

```
{project}/
├── {package_name}/
│   ├── __init__.py              # Package initialization
│   ├── main.py                  # Application entry point
│   └── settings.py              # Configuration (services only)
├── tests/
│   └── test_*.py                # Test files matching pattern
├── .github/
│   └── workflows/               # CI/CD workflows
├── Makefile                     # Build and development commands
├── pyproject.toml               # Project configuration
├── ruff.toml                    # Linting and formatting config
├── ty.toml                      # Type checker config
├── .pre-commit-config.yaml      # Pre-commit hooks
├── .gitignore                   # Git ignore rules
└── README.md                    # Project documentation
```

### File Naming Conventions

| Type | Pattern | Example |
|---|---|---|
| Python modules | snake_case | `user_service.py`, `api_handlers.py` |
| Test files | `test_*.py` | `test_user_service.py` |
| Classes | PascalCase | `UserService`, `ApiHandler` |
| Functions | snake_case | `get_user()`, `validate_email()` |
| Constants | SCREAMING_SNAKE_CASE | `MAX_RETRIES`, `DEFAULT_TIMEOUT` |
| Private members | `_leading_underscore` | `_internal_cache`, `_helper()` |

### Module Structure

Keep files under 200 lines for maintainability:

```python
"""Module docstring describing purpose."""

# Standard library imports
import os
import sys
from typing import Optional

# Third-party imports
import requests
from pydantic import BaseModel

# Local imports
from . import settings

# Constants
DEFAULT_TIMEOUT = 30
MAX_RETRIES = 3

# Classes
class MyClass:
    """Class docstring."""
    pass

# Functions
def my_function():
    """Function docstring."""
    pass

# Entry point
if __name__ == "__main__":
    pass
```

## Code Quality Standards

### Python Standards

**Python Version**: 3.10+ for generated projects, 3.12+ for template generator

**Encoding**: UTF-8 (no encoding declaration needed in Python 3)

**Line Length**: 88 characters (Ruff default, Black-compatible)

**Indentation**: 4 spaces (never tabs)

### Linting (Ruff)

Configuration in `ruff.toml`:

```toml
line-length = 88
target-version = "py310"

[lint]
select = ["E", "F", "I", "N", "W"]  # Error, Pyflakes, Import sort, Naming, Warnings
ignore = ["E203", "E501"]

[lint.isort]
profile = "black"
```

#### Enabled Rules

| Code | Category | Description |
|---|---|---|
| E | Error | PEP 8 errors (whitespace, indentation) |
| F | PyFlakes | Logic errors (unused imports, undefined names) |
| I | isort | Import sorting and organization |
| N | pep8-naming | Naming convention violations |
| W | Warning | PEP 8 warnings (blank lines, line breaks) |

#### Disabled Rules

- **E203**: Whitespace before colon (conflicts with formatters)
- **E501**: Line too long (handled by formatter)

### Import Organization

Order (enforced by isort):

1. Future imports: `from __future__ import annotations`
2. Standard library: `import os`, `from typing import ...`
3. Third-party: `import requests`, `from pydantic import ...`
4. Local: `from . import settings`, `from .models import User`

Blank line separates each group.

```python
from __future__ import annotations

import os
import sys
from typing import Optional

import requests
from pydantic import BaseModel

from . import settings
from .models import User
```

### Type Hints

All functions and methods must include type hints:

```python
def get_user(user_id: int) -> Optional[User]:
    """Retrieve user by ID."""
    # Implementation
    pass

class UserService:
    def create_user(self, name: str, email: str) -> User:
        """Create new user."""
        pass

    def list_users(self) -> list[User]:
        """List all users."""
        pass
```

### Docstrings

Use Google-style docstrings for classes and functions:

```python
def calculate_total(items: list[int], tax_rate: float = 0.1) -> float:
    """Calculate total with tax.

    Args:
        items: List of item prices.
        tax_rate: Tax percentage as decimal (default 0.1 for 10%).

    Returns:
        Total price including tax.

    Raises:
        ValueError: If tax_rate is negative.
    """
    if tax_rate < 0:
        raise ValueError("tax_rate must be non-negative")
    return sum(items) * (1 + tax_rate)

class UserService:
    """Service for managing user operations.

    Attributes:
        db: Database connection.
        cache: Optional cache instance.
    """

    def __init__(self, db: Database, cache: Optional[Cache] = None):
        """Initialize UserService.

        Args:
            db: Database connection instance.
            cache: Optional cache for performance.
        """
        self.db = db
        self.cache = cache
```

### Formatting (Ruff Format)

Automatically applied via `make check`:

- Consistent quote style (prefer double quotes)
- Consistent spacing around operators
- Consistent import formatting
- Consistent string formatting

### Type Checking (ty)

Static type verification configured in `ty.toml`. Run via `make check`:

```bash
make check  # Runs ty type checker
```

Fix type errors before committing.

## Git and Version Control

### Conventional Commits

All commits must follow conventional commit format:

```
<type>(<scope>): <subject>

<body>

<footer>
```

#### Types

| Type | Meaning | Version Bump |
|---|---|---|
| feat | New feature | Minor (0.1.0) |
| fix | Bug fix | Patch (0.0.1) |
| refactor | Code restructuring (no behavior change) | None |
| test | Test additions or fixes | None |
| docs | Documentation changes | None |
| chore | Maintenance tasks | None |
| ci | CI/CD configuration | None |
| style | Formatting changes | None |

#### Examples

```
feat(auth): add JWT token support
fix(api): resolve race condition in user lookup
refactor(core): simplify data validation logic
docs: update README with examples
test: add coverage for edge cases
chore: update dependencies
```

### Commit Message Guidelines

1. Use imperative mood ("add" not "added")
2. First line under 72 characters
3. Reference issues: `Closes #123`
4. Include motivation in body (why, not what)
5. Mark breaking changes: `BREAKING CHANGE: description`

```
feat(api): add webhook support

Implement webhook callbacks for event notifications.
Clients can now register webhooks for user and order events.

Closes #456
```

### Breaking Changes

Mark breaking changes to trigger major version bump:

```
feat(auth)!: require OAuth2 for all endpoints

BREAKING CHANGE: Authentication now requires OAuth2 tokens.
Old API keys no longer work. Migrate to new auth scheme.
```

## Pre-commit Hooks

Automatically run checks before each commit. Configuration in `.pre-commit-config.yaml`:

```yaml
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: "v5.0.0"
    hooks:
      - id: check-case-conflict      # Detect case conflicts in filenames
      - id: check-merge-conflict     # Detect merge conflict markers
      - id: check-toml               # Validate TOML syntax
      - id: check-yaml               # Validate YAML syntax
      - id: end-of-file-fixer        # Add newline at EOF
      - id: trailing-whitespace      # Remove trailing whitespace

  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: "v0.14.4"
    hooks:
      - id: ruff-check               # Lint and check
        args: [--exit-non-zero-on-fix]
      - id: ruff-format              # Format code
```

### Running Pre-commit

```bash
# Run once during setup
uv run pre-commit install

# Run manually on all files
uv run pre-commit run -a

# Run on staged files (automatic before commit)
uv run pre-commit run
```

## Testing Requirements

### Test Organization

```
tests/
├── test_main.py
├── test_models.py
├── test_services.py
└── conftest.py              # Shared fixtures
```

### Test File Naming

Test files follow pattern: `test_*.py` or `*_test.py`

### Minimum Coverage

- **Target**: > 80% code coverage
- **Critical paths**: 100% coverage required
- **Excluded**: `__main__` blocks, debug code

### Test Structure

```python
"""Tests for user service module."""

import pytest
from app.services import UserService
from app.models import User

class TestUserService:
    """Tests for UserService class."""

    @pytest.fixture
    def service(self):
        """Create UserService instance for testing."""
        return UserService()

    def test_create_user(self, service):
        """Test user creation."""
        user = service.create_user(name="John", email="john@example.com")
        assert user.name == "John"
        assert user.email == "john@example.com"

    def test_create_user_invalid_email(self, service):
        """Test user creation with invalid email."""
        with pytest.raises(ValueError):
            service.create_user(name="John", email="invalid")

def test_standalone_function():
    """Test standalone function."""
    result = some_function(42)
    assert result == 84
```

### Running Tests

```bash
# Run all tests
make test

# Run specific test file
uv run pytest tests/test_main.py

# Run with coverage report
uv run pytest --cov=app

# Run with verbose output
uv run pytest -v
```

## Code Review Checklist

Before committing, verify:

- [ ] Code passes `make check` (linting, formatting, type checking)
- [ ] Code passes `make test` (all tests pass, coverage > 80%)
- [ ] Commit message follows conventional format
- [ ] Type hints on all functions and methods
- [ ] Docstrings on all public classes and functions
- [ ] No hardcoded values (use configuration)
- [ ] No commented-out code (delete or create issue)
- [ ] Imports organized and sorted
- [ ] Line length <= 88 characters
- [ ] No trailing whitespace
- [ ] Descriptive variable names (avoid single letters except loops)

## Error Handling

### Exception Handling

```python
# Good: Specific exceptions
try:
    user = get_user(user_id)
except UserNotFoundError:
    logger.error(f"User {user_id} not found")
    raise
except ValueError as e:
    logger.error(f"Invalid user data: {e}")
    raise

# Bad: Generic exceptions
try:
    user = get_user(user_id)
except Exception:
    pass
```

### Logging

```python
"""Logging configuration and usage."""

import logging

logger = logging.getLogger(__name__)

# Usage
logger.debug("Debug information")
logger.info("Informational message")
logger.warning("Warning message")
logger.error("Error occurred", exc_info=True)
logger.critical("Critical error")
```

## Performance Considerations

1. **Avoid N+1 queries**: Load related data efficiently
2. **Cache appropriately**: Use caching for expensive operations
3. **Index databases**: Add indexes for frequently queried fields
4. **Use generators**: For large datasets to save memory
5. **Profile before optimizing**: Measure before making changes

## Security Standards

1. **No secrets in code**: Use environment variables
2. **Input validation**: Always validate user input
3. **SQL injection prevention**: Use parameterized queries
4. **XSS prevention**: Escape output in templates
5. **CSRF protection**: Implement anti-CSRF tokens
6. **Dependency updates**: Keep dependencies current
7. **Secret scanning**: Enable pre-commit secret detection

## Documentation Requirements

### Code Documentation

- Module docstrings at top of file
- Class docstrings describing purpose and attributes
- Function docstrings with args, returns, raises
- Complex logic commented with explanation
- No obvious comments (e.g., `i = i + 1  # increment i`)

### User Documentation

- README with quick start
- Setup instructions
- Configuration guide
- API documentation
- Examples and use cases
- Troubleshooting guide

## Tools and Commands

### Development

```bash
# Install dependencies and pre-commit hooks
make install

# Run code quality checks
make check

# Run tests
make test

# Build distribution
make build

# Run service (service projects only)
make serve
```

### Quality Gates

All commands must pass before commit:

```bash
make check  # Linting, formatting, type checking
make test   # All tests pass with coverage
```

## Useful Resources

- **PEP 8**: https://pep8.org/
- **Black Formatter**: https://github.com/psf/black
- **Ruff**: https://docs.astral.sh/ruff/
- **pytest**: https://docs.pytest.org/
- **Type Hints**: https://docs.python.org/3/library/typing.html
- **Conventional Commits**: https://www.conventionalcommits.org/
