---
name: implement
description: "Implement a piece of work based on a PRD or set of issues."
disable-model-invocation: true
---

Implement the work described by the user in the PRD or issues.

Follow the [@GUIDELINES.md](GUIDELINES.md) for all your work:

1. Think Before Coding: **Don't assume. Don't hide confusion. Surface tradeoffs.**
2. Simplicity First: **Minimum code that solves the problem. Nothing speculative.**
3. Surgical Changes: **Touch only what you must. Clean up only your own mess.**
4. Goal-Driven Execution: **Define success criteria upfront. Loop until verified.** 

**Use the /tdd skill wherever possible, at pre-agreed seams.**

Run typechecking regularly, single test files regularly, and the full test suite once at the end.

Once done, use /code-review to review the work.

Commit your work to the current branch.
