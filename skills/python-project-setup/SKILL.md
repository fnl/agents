---
name: python-project-setup
description: Set up a new Python project with uv, ruff, ty, pytest, pip-audit, and pre-commit hooks. Use when creating or bootstrapping a Python project, or when the user explicitly asks to add this toolchain to an existing one.
---

# Python project setup

Greenfield by default: run every step in a new project directory.
In an existing project, act only when the user explicitly asks to add the toolchain; then skip step 2 and merge config into the existing `pyproject.toml` rather than replacing it.

The Definition of Done (DoD) commands live in the Python rules; this skill makes them runnable.

## 1. Ensure uv

Check with `uv --version`. If missing, install with `pipx install uv`, or without pipx:

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

Then run `uv self update`.

## 2. Initialize the project

```bash
uv init --bare
uv venv
git init -b main
```

Replace `--bare` when the project type is known:
- `--app`: web servers, command-line interfaces (with a `main.py` at the root)
- `--lib`: functions and objects for other projects to consume (no entry point)
- `--package`: Python package development with a build system

## 3. Add the toolchain

```bash
uv add --dev ruff ty pytest pip-audit
uv run pip-audit
```

## 4. Configure ruff

Add to `pyproject.toml`:

```toml
[tool.ruff.lint]
select = [
    "E",   # pycodestyle errors
    "W",   # pycodestyle warnings
    "F",   # pyflakes
    "B",   # flake8-bugbear
    "S",   # flake8-bandit
    "C4",  # flake8-comprehensions
    "I",   # isort
    "N",   # pep8-naming
]
ignore = ["E501"]  # ruff format owns line length
```

## 5. Add pre-commit hooks

Create `.pre-commit-config.yaml` with local hooks so they use the project's locked tool versions:

```yaml
repos:
  - repo: local
    hooks:
      - id: ruff-format
        name: ruff format
        entry: uv run ruff format
        language: system
        types: [python]
      - id: ruff-check
        name: ruff check
        entry: uv run ruff check --fix
        language: system
        types: [python]
      - id: ty
        name: ty check
        entry: uv run ty check
        language: system
        types: [python]
        pass_filenames: false
      - id: pytest
        name: pytest
        entry: uv run pytest tests
        language: system
        types: [python]
        pass_filenames: false
```

Install with `uvx pre-commit install`, and add a README section stating that commits run these checks and how to install the hooks.

pytest exits with code 5 when it collects no tests, so the hook fails until the first test exists.

## 6. Verify

Done when `uvx pre-commit run --all-files` passes and every DoD command from the Python rules exits cleanly.

## Logging references

Consult when the project needs logging set up:
- https://docs.python.org/3/howto/logging.html
- https://docs.python.org/3/howto/logging-cookbook.html
