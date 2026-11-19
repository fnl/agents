---
name: implement
description: Implement work from a specification or issues. Use when implementing a planned piece of work.
---

Implement the work described by the user.

If the user passes a ticket reference, fetch it from the issue tracker and state its title before starting. If the reference is ambiguous, ask.

Follow the [@GUIDELINES.md](GUIDELINES.md) for all your work:

1. Think Before Coding: **Don't assume. Don't hide confusion. Surface tradeoffs.**
2. Simplicity First: **Minimum code that solves the problem. Nothing speculative.**
3. Surgical Changes: **Touch only what you must. Clean up only your own mess.**
4. Goal-Driven Execution: **Define success criteria upfront. Loop until verified.** 

Follow the user rules, always: `coding-standard.md`, plus each language rule matching the files you change. Repo-documented standards (e.g., `CODING_STANDARDS.md`, `CONTRIBUTING.md`) take precedence. Pass these rules explicitly to any sub-agent that writes code.

For an API behavior change, trace the changed value through the complete public path before declaring the slice complete: persistence or service result, handler, response model/schema, endpoint tests, integration tests where applicable, and user-facing documentation.

Call the Skill tool with "tdd" where possible, at pre-agreed seams.

Run typechecking regularly, single test files regularly, and the full test suite once at the end.

Once done, call the Skill tool with "code-review" to review the work.

Never add or commit your work, let the human operator review your work first,
unless the user explicitly asked you to commit your work without their review.
