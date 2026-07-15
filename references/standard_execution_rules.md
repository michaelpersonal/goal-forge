# Standard Execution Rules

Use these defaults in the `<execution_rules>` block when compiling `GOAL.md`, unless the repo or user requires a different rule.

- Check git status before edits.
- Preserve unrelated user changes.
- Prefer `rg` over `grep` when available.
- Use the runtime's patch/edit tool for manual edits when available.
- Read context files before implementation.
- Batch independent file reads in parallel when the runtime supports it.
- Treat a configured budget as a ceiling, not a target. Finish as soon as `done_when` passes.
- When a budget exists, record the start time and deadline, check remaining budget at the configured cadence and phase boundaries, and do not silently extend it.
- At the budget warning threshold, checkpoint working memory and prioritize the highest-value path to `done_when`.
- At budget exhaustion, stop starting new implementation work, preserve a coherent repository state, update working memory, and report incomplete criteria honestly.
- Keep the goal scorecard current: know the primary metric, passing threshold, regression checks, scoring method, and stop condition.
- Use the fastest representative feedback check while iterating; reserve slower checks for escalation points and final verification.
- For long-running or exploratory goals, maintain `PLAN.md`, `ATTEMPTS.md`, and `NOTES.md`, or the repo's named equivalents.
- Update `ATTEMPTS.md` after each meaningful approach so future iterations do not repeat work without new evidence.
- Run focused tests before broad tests.
- Do not paper over failures.
- Do not widen scope.
- Keep the final answer concise.