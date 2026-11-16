---
name: cto
description: CTO orchestration from a local project and task to a verified pull request ready for human review.
disable-model-invocation: true
---

# CTO (Chief Technology Officer)

Orchestrate development through independent sessions; deliver a verified pull request (PR) ready for human review.
Example: `/cto ~/src/shop "Fix duplicate checkout submissions"` or `/cto owner/shop #123`.
Install this skill in the harness's user-level skill directory to invoke it from anywhere.

## Operating contract

- Use Git, GitHub's `gh` command or available integration, and installed agent commands. No extensions or custom agent definitions are required.
- Resolve linked skills relative to this skill's installed location, not the target repository. Read [git](../git/SKILL.md) before Git operations.
- Load project instructions and standards in each session. Keep a short handoff outside tracked files: repository, issue, acceptance criteria, base commit, worktree, branch, checks, session references, findings, and approval scope.
- Run stages in order; do not mark a stage complete without its evidence. Preserve unrelated changes; never reset, stash, clean, or delete existing work to proceed.
- Only the orchestrator commits or pushes. Never push without explicit human approval for this branch and scope; an earlier explicit approval counts. Never merge or modify the base branch directly.

## 1. Discover the project

Accept a local path, repository name, or owner/name plus a task or issue. Resolve an explicit path first; otherwise inspect known workspace roots and common checkout directories using bounded filesystem searches, including `.git` files for worktrees.
Verify candidates with `git -C <path> rev-parse --show-toplevel` and `git -C <path> remote -v`; match the requested GitHub repository, not just its directory name.
If absent or ambiguous, ask for the local path; do not silently clone or select a namesake. Use absolute paths or explicit working directories for every later command.

**Done:** one verified repository, GitHub owner/name, task, and initial `git status --short` and `git worktree list` recorded.

## 2. Investigate and plan

Read repository instructions, relevant code, tests, and check commands. Establish scope, observable acceptance criteria, implementation steps, and required verification commands; record inapplicable checks with reasons.
Reuse the supplied issue or search for an existing matching issue before creating one. Create or update its implementation plan using `gh issue` or the available GitHub integration, preserving unrelated issue content.
Read [implement](../implement/SKILL.md) and [tdd](../tdd/SKILL.md). Agree test seams with the user when new tests are needed unless already explicitly agreed; resolve material ambiguity before implementation.
Confirm the intended base branch and record the fetched remote base commit. Record a retry budget: at most three implementation/check/review cycles by default.

**Done:** issue URL, acceptance criteria, scope, applicable standards, verification commands, and any required seam agreement recorded.

## 3. Isolate the work

Choose an unused feature branch and worktree path. Run `git -C <repo> worktree add -b <branch> <absolute-worktree> <base-commit>` without switching the original checkout.
Verify the worktree root, branch, clean status, and starting commit; retain the original checkout's status for comparison.

**Done:** dedicated clean worktree on the feature branch at the recorded base, with existing work preserved.

## 4. Delegate implementation

Start a fresh session of an installed coding agent inside the worktree. Inspect its command help for supported invocation options; do not assume Pi, Codex, or Claude Code share flags or session controls.
Pass the issue/plan, acceptance criteria, absolute worktree and skill paths, project rules, and check commands. Require the implementation session to invoke `implement` before coding and use `tdd` where applicable. If the harness lacks a skill invocation mechanism, require it to load and execute the linked `implement/SKILL.md` directly; if unavailable, stop and report the blocker.
Tell the implementer to leave changes uncommitted and report changed files, checks, and blockers; the orchestrator owns review checkpoints and the independent review below.
If sessions cannot be launched, provide the exact working directory and handoff prompt for the human to start one; pause dependent work. Do not substitute self-review for independence.

**Done:** implementation session confirms it ran `implement`, has finished with changes in the isolated worktree, and reports evidence for every acceptance criterion or blocker.

## 5. Verify and checkpoint

Run the project's required tests, lint, type checking, build, and other relevant checks inside the worktree. Inspect the full diff and check acceptance criteria, including any manual checks.
Feed failures back to the implementation session and rerun affected checks plus required final suites after fixes. Never suppress failures, weaken checks, or call an unavailable check a pass.
After checks pass, stage only task changes and create a local checkpoint commit following `git`; invoking `/cto` authorizes these local commits. This supplies the committed diff required by `code-review`.

**Done:** required checks pass, inapplicable checks are explained, acceptance evidence is recorded, and all task changes are committed with a clean worktree.

## 6. Independently review and iterate

Start a fresh review session in the worktree using [code-review](../code-review/SKILL.md). Supply the recorded base commit, current commit, issue contents, standards paths, and check results; require read-only review of the full task diff.
Use the project's issue-tracker instructions when present; otherwise supply the fetched GitHub issue as the spec and the GitHub access method explicitly, without requiring project scaffolding.
Preserve `code-review`'s separate Standards and Spec reports. Where native sub-agents are unavailable, use separate fresh ordinary sessions for its two axes, sequentially if necessary, then aggregate their reports.
Send actionable findings to the implementation session. Repeat verification, local commits, and fresh review after changes; never reuse a passing review for changed content.
After three unsuccessful cycles, or an unrecoverable blocker, stop and report evidence and unresolved findings. Keep the worktree and commits; do not push or claim readiness.

**Done:** both review axes cover the current commit and acceptance criteria, all actionable findings are resolved, required checks pass, and the worktree is clean.

## 7. Deliver for human review

Summarize the final diff, commits, checks, review outcome, issue, and proposed remote/branch. Request explicit push approval if none already covers this work; wait for it before any push.
Push only the feature branch to the verified remote, then create a non-draft PR against the recorded base branch using `gh pr create` or the GitHub integration. Link the issue and include validation and any limitations. If resuming an existing draft, mark it ready only after the preceding gates pass.
If pushing or PR creation fails, report the actual remote state and resume only the failed step; check for an existing PR before retrying creation. Never merge it.
Monitor required remote checks on the pushed commit until they pass. Route failures through implementation, verification, and fresh review within the remaining retry budget, then push fixes only within explicit approval scope. If checks cannot finish or failures remain, report a blocker and keep delivery incomplete; record when no remote checks are configured.

**Done:** required local and remote checks pass for the reviewed head commit; verify the PR URL, non-draft status, base, and head commit. Return the URL, concise change/validation summary, and retained worktree path. Never claim production readiness with pending or failed required checks.
