---
paths:
  - "**/*.py"
  - "pyproject.toml"
---

# Python development rules
These rules describe how you must develop any Python projects.
Use the `uv` package & project manager to operate all projects.

If `uv` is not available in your environment, you can install it with `pipx` if available:
```bash
pipx install uv
```
Or with curl, as `uv` can later update itself:
```
curl -LsSf https://astral.sh/uv/install.sh | sh
```

Once in a dedicated directory, the project can be set up with:
```bash
uv self update # ensure latest uv
uv init --bare # create pyproject.toml file
uv venv # create .venv/ setup
git init -b main
```
The `uv init` step might optionally take `--app`, `--lib`, or `--package` as argument instead of `--bare` if you know already that it will be more appropriate:
- `--app`: web servers, command-line interfaces (with a main.py file at the root)
- `--lib`:  functions and objects for other projects to consume (no entry point)
- `--package`: Python package development with a build system

## Basic rules
- Source code is in a `./$CODE/` directory (e.g., `src/`, `mypack/`, etc.)
- Tests are in a parallel `./tests/` directory
- All tools must be run via `uv run` (do not invoke tools directly).
- All code is **strictly type-checked**, no excuses.
- **Never** add import statements anywhere else than the header of a module.

## Required tools
- ruff (code formatting, linting, including bandit-like security audit)
- ty (type safety)
- pytest (test runner)
- pip-audit (dependencies audit)

Install tool-chain into the project environment with `uv`:
```bash
uv add --dev ruff ty pytest pip-audit
````

### Format the code
Ruff will automatically fix any code formatting issues.
```bash
uv run ruff format $CODE tests
```

### Lint and auto-fix where possible
Use an extensive ruleset for security, bugs, and other poor coding habits.
Enforce PEP8 naming with N, too.
Ruff formatting handles line lengths, so that can be ignored.
```bash
uv run ruff check $CODE tests --select E,F,W,S,B,C4,I,N --ignore E501 --fix
```

Configure this in the `pyproject.toml` file:
```toml
[tool.ruff]
select = [
    "E",   # pycodestyle errors (default)
    "W",   # pycodestyle warnings (default)
    "F",   # pyflakes
    "B",   # flake8-bugbear
	"S",   # flake8-bandit
    "C4",  # flake8-comprehensions
    "I",   # isort
    "N",   # pep8-naming
]
```

### Write type-safe code
The use of `Any` is a smell. Only use `Any` if there is a technically clear justification.
```bash
uv run ty $CODE
```

- `ty` suppression syntax: `# type: ignore` (plain — bracketed error codes are not supported).

### Do test-driven development
Write failing tests before implementation. Follow [@testing](Tech/Software%20Development/Skills/tdd/SKILL.md) guidelines.  And run the tests with `pytest`:
```bash
uv run pytest tests
```

### Audit dependencies
Only needed when you add a new package of once in a while, to ensure no dependencies have known vulnerabilities.
```bash
uv run pip-audit
```

## Code standards
- Keep functions small (a few lines), single-purpose, and easy to test (if public).
- Ensure all public functions, interfaces, and non-private variables are type-checked and tested.
- Always strive to reduce the size of the public API.
- Never make an exception like "if TYPE_CHECKED: ...", assume all code is type-checked.
- Always prefer expressive code over adding comments.
- Extract logic into short, well-named functions or classes that **describe intent**.
- Only comment when the logic still is genuinely hard to understand.
- Never add imports anywhere except at the head of the module.
- Follow the SOLID principles when designing or writing code: Single Responsibility, Open/Closed, Liskov Substitution, Interface Segregation, Dependency Inversion.
- Before writing manual conversion glue to bridge a third-party library with stdlib, check for a compat/interop submodule (e.g. `<pkg>.compat`).
- Closed sets of string values used across call sites (error codes, statuses, taxonomies) should be an `Enum`, never repeated literals.

**Bad: opaque condition**
```python
if user.role == 'admin' and \
   user.verified and \
   not user.suspended and \
   time.time() < user.expires_at:
    show_dashboard()
```

**Good: expressive logic**
```python
class User:
	...
    def can_access_dashboard(self) -> bool:
        return (self.role == 'admin'
                and self.verified
                and not self.suspended
                and not self._has_session_expired())

    def _has_session_expired(self) -> bool:
        return time.time() >= self.expires_at

if user.can_access_dashboard():
    show_dashboard()
```

### Error handling
- Define custom error types/classes
- Never silently catch errors without handling
- Use specific catch blocks instead of catching all errors
- Log errors with sufficient context for debugging (e.g. user ID, document ID, relevant settings)

### Logging
- Provide context through `LoggerAdapater` and `Filter` classes.
- Capture and track context with the `contextvars` StdLib package.
- Refer to https://docs.python.org/3/howto/logging.html and https://docs.python.org/3/howto/logging-cookbook.html for proper logging practices.

### Dependencies
- ONLY ADD DEPENDENCIES WHEN NEEDED.
- Do not add dependencies just because they are planned.
- Only add a dependency when writing code that needs it.
- Only add dependencies ad hoc if the project does not compile or start because of a missing dependency.

### Documentation
- When creating code blocks in documentation, do not add comments inside the code blocks; Add the comments outside, in the text area.

## Definition of Done
After making a test green and the refactoring is done, ensure all tools pass or fix any issues.
Note that `ruff` has to be configured in the `pyproject.toml` file or you need to use the extended arguments to select rulesets shown above in the linting section.

```bash
uv run ruff format $CODE tests
uv run ty $CODE
uv run ruff check $CODE tests --fix
uv run pytest tests
uv run pip-audit
```

### Git pre-commit hooks to test DoD
Instead of running the above checks every time manually, create git commit hooks for ruff, ty, and pytest.
Set the proper hooks up using `uvx pre-commit` and a `.pre-commit-config.yaml` configuration, and mention that approach in the project's README.
Run pip-audit only when you add a dependency.