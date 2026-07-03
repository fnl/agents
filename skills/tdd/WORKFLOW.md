# Workflow

## 1. Planning

When exploring the codebase, use the project's domain glossary so that test names and interface vocabulary match the project's language, and respect ADRs in the area you're touching.

Before writing any code:

- [ ] Confirm with user what interface changes are needed
- [ ] Confirm with user which behaviors to test (prioritize)
- [ ] Identify opportunities for [deep modules](deep-modules.md) (small interface, deep implementation)
- [ ] Design interfaces for [testability](interface-design.md)
- [ ] List the behaviors to test (not implementation steps)
- [ ] Get user approval on the plan

Ask: "What should the public interface look like? Which behaviors are most important to test?"

**You can't test everything.** Confirm with the user exactly which behaviors matter most. Focus testing effort on critical paths and complex logic, not every possible edge case.

## 2. Tracer Bullet

Write exactly ONE behavior or acceptance criteria that confirms ONE thing about the system.

```
RED:   Write behavior test for core acceptance criteria → tracer bullet fails
GREEN: Write minimal tests and code in red-green loop → tracer bullet passes
```

This is your tracer bullet - it is designed so that when it passes, it proves that particular path works end-to-end.

Typically, the tracer bullet will be a [Gherkin](Tech/Software%20Development/Skills/to-prd/Gherkin-syntax.md) **Scenario** using a GIVEN-WHEN-THEN structure.

## 3. Red-Green Testing Loop

For each functionality or aspect needed to make your tracer bullet green:

```
RED:   Write next test → test fails
GREEN: Minimal code to pass → test passes
```

Rules:

- One test at a time
- Only enough code to pass current test
- Don't anticipate future tests
- Keep tests focused on observable behavior
- With as few assertions as possible
- Do not add code that is not covered by the test

If you discover the need to validate additional behavior, track those findings as new issues on the issue tracker (see the `to-issue` skill on how to write issues).
Don't just copy the expected values by running the test without understanding them.

## 4. Refactor

After all tests pass, look for [refactor candidates](refactoring.md):

- [ ] Extract duplication (DRY code)
- [ ] Deepen modules (move complexity behind simple interfaces)
- [ ] Apply SOLID principles where natural
- [ ] Consider what new code reveals about existing code
- [ ] Run tests after each refactor step

**Never refactor while RED.** Get to GREEN first.

## 5. Commit

**Commit** all the changes you made once a test is green and the code is refactored.

Then keep working out and implementing every acceptance/behavior test on your list one-by-one as your tracer bullets.