---
description: Python toolchain, typing, and Definition of Done.
applyTo: "**/*.py, **/pyproject.toml"
paths:
  - "**/*.py"
  - "pyproject.toml"
---

# Python development rules
Explicit project instructions and established repository conventions take precedence.

- Use `uv` for everything and run every tool via `uv run`.
- Set up new projects with the `python-project-setup` skill. Add the toolchain or its config to an existing project only when instructed.
- Source lives in `$CODE/` (e.g., `src/`, `mypack/`); tests live in a parallel `tests/` directory.
- All code is strictly type-checked with `ty`. Use `Any` only with a stated technical justification.
- Suppress `ty` findings with `# ty: ignore[<rule>]`, always naming the rule.
- Write failing tests first, following the `tdd` skill.
- Logging: provide context through `LoggerAdapter` and `Filter` classes, tracked with `contextvars`.

## Definition of Done
After a test turns green and the refactoring is done, run these and fix every finding:

```bash
uv run ruff format $CODE tests
uv run ruff check $CODE tests --fix
uv run ty check $CODE
uv run pytest tests
```

After adding or changing a dependency, also run `uv run pip-audit`.