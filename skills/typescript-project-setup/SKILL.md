---
name: typescript-project-setup
description: Set up a new TypeScript project with pnpm, prettier, ESLint, tsc, vitest, and Husky pre-commit hooks. Use when creating or bootstrapping a TypeScript project, or when the user explicitly asks to add this toolchain to an existing one.
---

# TypeScript project setup

Greenfield by default: run every step in a new project directory.
In an existing project, act only when the user explicitly asks to add the toolchain; then skip step 2 and merge config into the existing files rather than replacing them.

The Definition of Done (DoD) commands live in the TypeScript rules; this skill makes them runnable.

## 1. Ensure pnpm

Check with `pnpm --version`. If missing, run `corepack enable pnpm`.

## 2. Initialize the project

```bash
pnpm init
pnpm add -D typescript
pnpm exec tsc --init
git init -b main
```

Confirm `tsconfig.json` has `"strict": true`.

## 3. Add the toolchain

```bash
pnpm add -D eslint @eslint/js typescript-eslint eslint-plugin-security prettier vitest
pnpm audit
```

## 4. Configure ESLint

Create `eslint.config.mjs` at the project root:

```javascript
// @ts-check
import eslint from '@eslint/js';
import { defineConfig } from 'eslint/config';
import tseslint from 'typescript-eslint';
import security from 'eslint-plugin-security';

export default defineConfig(
  eslint.configs.recommended,
  tseslint.configs.strict,
  tseslint.configs.stylistic,
  {
    files: ['**/*.{js,ts}'],
    plugins: { security },
    rules: { ...security.configs.recommended.rules },
  },
);
```

## 5. Add pre-commit hooks

```bash
pnpm add -D husky
pnpm exec husky init
```

`husky init` creates `.husky/pre-commit` and adds `"prepare": "husky"` to `package.json`, so hooks install on `pnpm install`.
Replace the sample command in `.husky/pre-commit` with the DoD commands from the TypeScript rules, and add a README section stating that commits run these checks.

vitest fails when it finds no test files, so the hook fails until the first test exists.

## 6. Verify

Done when every DoD command from the TypeScript rules exits cleanly and `sh .husky/pre-commit` passes.
