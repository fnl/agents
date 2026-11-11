---
paths:
  - "**/*.{py,pyi,ipynb,ts,tsx,mts,cts,js,jsx,mjs,cjs,vue,svelte,astro,c,h,cc,cpp,cxx,hpp,hh,hxx,ixx,cppm,rs,cs,csx,rb,rake,gemspec,ru,php,phtml,go,java,kt,kts,swift,scala,sc,dart,lua,pl,pm,t,jl,groovy,gradle}"
---

# Coding Standards

Rules for working with source code.
Explicit project instructions and established repository conventions take precedence.
Skip rules that do not apply to the language at hand.

## Context first
- Search for existing functionality before implementing new helpers or utilities; Reuse or extend suitable existing code.
- Obtain confirmation before making destructive or irreversible operations to existing work; A push needs confirmation, a local commit does not.
- When explaining or referring to existing code, always support factual claims with `path:line` references.

## Scope & Changes
- Make the smallest change that satisfies the request; Avoid unrelated modifications.
- Preserve behavior outside the requested change, including existing edge cases.
- Separate structural refactoring from behavioral changes: When both are needed, make the structural change first where practical and keep the two in separate commits.
- Remove code rendered obsolete by a change, but do not perform unrelated cleanup.
- Commit locally in small, self-contained steps; each commit must pass the checks.

## Design & Clarity
- Give each function, class, and module one cohesive responsibility.
- Order *new* code you write top-down: public and high-level functions first, the helpers they call below them. Where the language requires definition before use, follow the language.
- Extract a named function when a block could use a comment to explain what it does, or has more lines than can be grasped quickly.
- Prefer explicit control flow and guard clauses over clever expressions and deep nesting.
- Encapsulate implementation details behind minimal interfaces at meaningful boundaries.
- Keep business logic free of direct infrastructure, I/O, and framework calls, ideally at module level.
- Keep business logic in modules that import no infrastructure, I/O, or framework code. Dependencies point inward: outer layers (transport, persistence, UI) depend on the domain, never the reverse.
- When business logic needs an external capability, define the interface in the domain and implement it in an outer layer.
- Avoid `any`, unchecked casts, and type-checker suppressions; when one is unavoidable, state the reason in an adjacent comment.
- Introduce abstractions only when they solve a concrete, present problem.
- Briefly report code you touched that violates these principles; do not fix it unasked.

## Correctness & Testing
- Annotate public function signatures and exported declarations with types; rely on inference for local variables.
- Test observable behavior through public interfaces rather than implementation details.
- Do not call, patch, or assert on private members in tests, and do not widen visibility to make something testable.
- Add or update focused tests for changed behavior and relevant edge cases. If testing is infeasible, report why.
- Never weaken, skip, or remove tests merely to make checks pass; Update obsolete tests when requirements change.

## Errors & Observability
- Never silently discard errors or add fallbacks that conceal failures.
- Handle expected failures explicitly; catch specific exceptions where recovery, translation, or contextual reporting is possible.
- Define custom exception types for domain-specific failures; use built-in types for argument and programming errors. When wrapping an error, preserve the original cause.
- Log events that cannot be reconstructed from code and inputs: external calls and queries, state changes, consequential runtime decisions, and handled errors.
- Log each error once, where it is handled, with enough context to act on.
- Never log secrets or personal data.
- Follow the project's existing logging conventions; where none exist, use structured logging (key-value fields, not interpolated strings).
- If the project uses tracing, instrument new external calls and request handlers consistently.
- For short stand-alone scripts, plain output to stdout and stderr is sufficient.

## Dependencies & Imports
- Use the standard library or existing dependencies when sufficient.
- Add external dependencies only when they provide a concrete benefit that outweighs implementation and maintenance costs.
- Place imports at file scope. Use local imports only for a technical reason (circular, optional, or deferred dependencies) and note the reason beside the import.
- Always report if you added a new dependency, explaining the rationale.

## Verification & Checks
- Before a commit, run all unit tests, the linter, and type checker.
- If checks fail, investigate whether the changes caused the failure; Do not modify unrelated code solely to make checks pass.
- Report which checks passed, failed, or could not be run; Never claim verification that was not performed.
