---
name: tdd
description: Test-driven development. Use when the user wants to build features or fix bugs test-first, mentions "red-green-refactor", or wants integration tests.
---

# Test-Driven Development

TDD is the red → green loop. This skill is the reference that makes that loop produce tests worth keeping: what a good test is, where tests go, the anti-patterns, and the rules of the loop. Every section applies on every cycle: consult them before and during the loop, not after.

When exploring the codebase, read `CONTEXT.md` (if it exists) so test names and interface vocabulary match the project's domain language, and respect ADRs in the area you're touching.

## What a good test is

Tests verify behavior through public interfaces, not implementation details. Code can change entirely; tests shouldn't. A good test reads like a specification: "user can checkout with valid cart" tells you exactly what capability exists, and it survives refactors because it doesn't care about internal structure.

See [tests.md](tests.md) for examples and [mocking.md](mocking.md) for mocking guidelines.

## Seams: where tests go

A **seam** is the public boundary you test at: the interface where you observe behavior without reaching inside. Tests live at seams, never against internals.

**Test only at pre-agreed seams.** Before writing any test, write down the seams under test and confirm them with the user. No test is written at an unconfirmed seam. You can't test everything, so agreeing the seams up front is how testing effort lands on the critical paths and complex logic instead of every edge case.

Ask: "What's the public interface, and which seams should we test?"

When the shape of that interface is itself in question (how deep the module is, where the seam belongs, what the interface should expose), call the Skill tool with "codebase-design" for the vocabulary. It is the shared source of the module, interface, depth, seam, adapter, leverage and locality terms, and it is a reference to consult, not a session to run.

## Anti-patterns

- **Implementation-coupled**: mocks internal collaborators, tests private methods, or verifies through a side channel (querying the database instead of using the interface). The tell: the test breaks when you refactor but behavior hasn't changed.
- **Tautological**: the assertion recomputes the expected value the way the code does (`expect(add(a, b)).toBe(a + b)`, a snapshot derived by hand the same way, a constant asserted equal to itself), so it passes by construction and can never disagree with the code. Expected values must come from an independent source of truth: a known-good literal, a worked example, the spec.
- **Horizontal slicing**: writing all tests first, then all implementation. Bulk tests verify _imagined_ behavior: you test the _shape_ of things rather than user-facing behavior, the tests go insensitive to real changes, and you commit to test structure before understanding the implementation. Work in **vertical slices** instead: one test → one implementation → repeat, each test a **tracer bullet** that responds to what the last cycle taught you.

## Rules of the loop

- **Red before green.** Write the failing test first, then only enough code to pass it. Don't anticipate future tests or add speculative features.
- **One slice at a time.** One seam, one test, one minimal implementation per cycle.
- **Refactoring is not part of the loop.** It belongs to the review stage (see the `code-review` skill), not the red → green implementation cycle.

## Workflow

1. Planning: Ask: "What should the public interface look like? Which behaviors are most important to test?" **You can't test everything.** Focus testing effort on critical paths and complex logic, not every possible edge case.
2. Tracer Bullet: Write exactly ONE behavior or acceptance criteria that confirms ONE thing about the system. Typically, the tracer bullet will be a [Gherkin](Tech/Software%20Development/Skills/to-prd/Gherkin-syntax.md).
3. Red-Green Testing Loop: For each functionality or aspect needed to make your tracer bullet green.
4. Refactor: After all tests pass, look for [refactor candidates](refactoring.md). **Never refactor while RED.** Get to GREEN first.
5. Commit: All the changes you made once a test is green and the code is refactored.

## Checklist Per Red/Green Cycle

```
[ ] Test describes behavior, not implementation
[ ] Test uses public interface only
[ ] Test would survive internal refactor
[ ] Code is minimal for this test
[ ] No speculative features added
```

## Testing rules

- ONLY ASSERT WHAT THE TEST OR SCENARIO CLAIMS TO VALIDATE.
- IDEAL TESTS HAVE EXACTLY ONE ASSERTION, no less and no more.
  - For example, if you are testing an HTTP endpoint is returning content type JSON, don't also assert the status code.
- Test the behavior of the public interface only: exported functions, methods, HTTP handlers, or CLI commands, not private helpers.
- Only write just enough code to make the the test or scenario pass.
- Use descriptive names and simple language so anyone can understand the intent without reading the code.
- Tests must be independent of each other and test business rules and requirements, not implementation logic.
- Mock external dependencies, not internal logic, and focus on behavior not implementation.
  - Never mock or patch the function/unit the test claims to verify — if `foo()` is under test, do not mock `foo`; only mock what `foo` calls out to.
- Tests should run in milliseconds, to keep the test suite fast.
  - Tests over a few dozen milliseconds should be set aside and not run every time (e.g., end-to-end integration and acceptance tests that get run only before frequent deployments).
- If you add new tests, review how redundant the setup is with other tests, and clean up the code.
  - Only worry about the concrete aspects under test and keep test code DRY.
