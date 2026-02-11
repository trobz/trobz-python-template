# Documentation Index

Welcome to the Trobz Python Template documentation. This index helps you find the information you need.

## Quick Navigation

### Getting Started

New to the template? Start here:

1. **[README.md](../README.md)** - Project overview, features, and quick start

### For Developers

Building with the template:

2. **[code-standards.md](code-standards.md)** - Code quality standards and conventions
3. **[codebase-summary.md](codebase-summary.md)** - Project structure reference


## Document Overview

### README.md (Main Entry Point)
**Purpose:** Project introduction and feature overview
**Length:** 344 lines
**Audience:** Everyone
**Key Sections:**
- Features overview
- Quick start guide
- Project type details (CLI, FastAPI, Flask)
- Configuration guide
- Development workflow
- GitHub Actions integration
- Support and resources

### docs/codebase-summary.md
**Purpose:** Codebase structure and organization reference
**Length:** 288 lines
**Audience:** Developers, architects
**Key Sections:**
- Directory structure
- Key components
- Template variables
- Conditional rendering
- Generated project structure
- Build system overview

### docs/code-standards.md
**Purpose:** Coding conventions and quality standards
**Length:** 532 lines
**Audience:** Developers
**Key Sections:**
- Project structure and organization
- File naming conventions
- Python standards (PEP 8)
- Linting rules (Ruff)
- Import organization
- Type hints and docstrings
- Testing requirements (80%+ coverage)
- Git workflow (conventional commits)
- Pre-commit hooks
- Error handling patterns
- Security standards

## By Topic

### Template Usage
- [codebase-summary.md](codebase-summary.md)

### CLI Applications
- [README.md - CLI Application](../README.md#cli-application)

### Web Services
- [README.md - FastAPI Service](../README.md#service-application-fastapi)
- [README.md - Flask Service](../README.md#service-application-flask)

### Code Quality
- [code-standards.md](code-standards.md)
- [code-standards.md - Linting (Ruff)](code-standards.md#linting-ruff)
- [code-standards.md - Testing](code-standards.md#testing-requirements)
- [code-standards.md - Pre-commit Hooks](code-standards.md#pre-commit-hooks)

### Development Workflow
- [code-standards.md - Code Organization](code-standards.md#code-organization)
- [code-standards.md - Git and Version Control](code-standards.md#git-and-version-control)
- [README.md - Development Workflow](../README.md#development-workflow)

## Common Tasks

### I want to create a new CLI project
1. Read [README.md - Quick Start](../README.md#quick-start)
2. Follow [deployment-guide.md - Using the Template](deployment-guide.md#using-the-template)
3. Refer to [README.md - CLI Application](../README.md#cli-application) for examples
4. Check [deployment-guide.md - CLI Development](deployment-guide.md#cli-development)

### I want to create a new web service
1. Read [README.md - Quick Start](../README.md#quick-start)
2. Follow [deployment-guide.md - Using the Template](deployment-guide.md#using-the-template)
3. Choose framework: [README.md - FastAPI Service](../README.md#service-application-fastapi) or [Flask Service](../README.md#service-application-flask)
4. Check [deployment-guide.md - Service Development](deployment-guide.md#service-development)

### I need to deploy a generated project
1. Read [deployment-guide.md - Service Deployment](deployment-guide.md#service-deployment)
2. Choose deployment method (Gunicorn, Docker, Systemd, Heroku, AWS, etc.)
3. Configure environment: [deployment-guide.md - Configuration](deployment-guide.md#configuration-for-deployment)
4. Set up CI/CD: [deployment-guide.md - GitHub Actions](deployment-guide.md#github-actions-configuration)

### I need to understand code standards
1. Read [code-standards.md](code-standards.md)
2. Check specific sections: [naming conventions](code-standards.md#file-naming-conventions), [linting](code-standards.md#linting-ruff), [testing](code-standards.md#testing-requirements)
3. Review [git workflow](code-standards.md#git-and-version-control) and [conventional commits](code-standards.md#conventional-commits)

### I'm having issues with the template
1. Check [deployment-guide.md - Troubleshooting](deployment-guide.md#troubleshooting)
2. Look for your issue in the common problems section
3. Read [README.md - Support](../README.md#support) for additional help
