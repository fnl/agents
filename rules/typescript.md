---
description: TypeScript toolchain, code standards, and Definition of Done.
applyTo: "**/*.ts, **/eslint.config.mjs, **/tsconfig.json, **/package.json"
paths:
  - "**/*.ts"
  - "eslint.config.mjs"
  - "tsconfig.json"
  - "package.json"
---

# TypeScript development rules
Explicit project instructions and established repository conventions take precedence.

- Use `pnpm` for everything and run every tool via `pnpm exec`.
- Set up new projects with the `typescript-project-setup` skill. Add the toolchain or its config to an existing project only when instructed.
- Source lives in `$CODE/` (e.g., `src/`, `mypack/`); tests live in `tests/`.
- Keep `"strict": true` in `tsconfig.json`.
- Write failing tests first, following the `tdd` skill.

## Code standards

### Helper functions

Use regular functions, not const arrows:

```typescript
function goodHelper(param: string): RVal {
  ...
}

const badHelper = (param: string): RVal => ({...})
```

### Async/await patterns

- Prefer async/await over Promise chains
- Always return Promises properly from async functions
- Handle Promise rejections appropriately
- Use `Promise.all()` for parallel operations when applicable

### Regex Iteration

Prefer `String.matchAll` with `for...of` instead of `RegExp.exec` inside a `while` loop.

```typescript
// Good
for (const match of text.matchAll(regex)) {
  // use match
}

// Avoid
let match: RegExpExecArray | null
while ((match = regex.exec(text)) !== null) {
  // use match
}
```

### Interface Naming

Do not use `I` prefixes for interface names. Prefer descriptive names like `TranslatorService` instead of `ITranslatorService`.

## Definition of Done
After a test turns green and the refactoring is done, run these and fix every finding.
If `package.json` already has a combined script (e.g., `pnpm dev` via concurrently), use it instead; skip the separate prettier step when ESLint runs prettier.

```bash
pnpm exec prettier --write $CODE tests
pnpm exec eslint --fix $CODE tests --max-warnings 0
pnpm exec tsc -p tsconfig.json --build
pnpm exec vitest run tests
```

`tsc --build` emits JS so vitest runs against the latest TS code.
After adding or changing a dependency, also run `pnpm audit`.

