Use Orca's orchestration skill to implement the specification in GitHub issue AS INSTRUCTED.

1. Read the specification and all linked implementation issues using the GitHub CLI.
2. Build an Orca task dependency graph from the GitHub issue dependencies.
3. Start up to 3 independent subagents
 in separate Orca worktrees.
4. Each worker should implement one issue, run appropriate tests, and report its changes and verification results.
5. Dispatch dependent issues only after their prerequisites have been successfully completed and integrated.
6. Request human approval for architectural changes, destructive operations, and merging.
7. Report completed work, blockers, and PR links. Never merge automatically.

Use Orca orchestration, not Pi's native subagent manager. Load the current Orca orchestration guide first.