# Trobz Python Template

[Copier](https://github.com/copier-org/copier) template for Python projects managed by [uv](https://github.com/astral-sh/uv).

## Features

- [uv](https://github.com/astral-sh/uv) setup, with pre-defined `pyproject.toml`
- Pre-configured tools for code formatting, quality analysis and testing:
  [ruff](https://github.com/charliermarsh/ruff),
- Tests run with [pytest](https://github.com/pytest-dev/pytest) and plugins, with [coverage](https://github.com/nedbat/coveragepy) support
- Support for GitHub workflows
- Auto-generated `CHANGELOG.md` from Git (conventional) commits

## Quick Start

```bash
copier copy --trust "https://github.com/trobz/trobz-python-template.git" /path/to/your/new/project
```
