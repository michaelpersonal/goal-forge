# Goal Budget Contract

Use a budget when the user limits how long, how much, or how many attempts a `/goal` run may consume. For long-running goals, make the budget explicit even when the decision is `none` so the runtime contract is not ambiguous.

A budget is a ceiling, not a target and not a completion criterion. The goal should finish as soon as `done_when` passes.

## Canonical Time Budget

Translate natural-language requests such as "5 hour budget max" into a structured block:

```xml
<budget>
max_elapsed_time: 5h
warning_at: 4h30m
check_interval: 15m
on_warning: checkpoint_and_prioritize
on_exhaustion: checkpoint_and_stop
extension_policy: explicit_user_approval
</budget>
```

Use compact normalized durations such as `30m`, `5h`, or `2h30m`. Preserve stricter user wording. Do not round a limit upward.

## Required Semantics

- `max_elapsed_time` is wall-clock time measured from the start of the `/goal` run.
- Record the start time and calculated deadline in `PLAN.md` when working memory is enabled.
- Check the remaining budget at least at the configured interval and before phase changes, expensive steps, broad test suites, or strategic pivots.
- `warning_at` must be earlier than `max_elapsed_time`. At the warning threshold, checkpoint working memory, reassess the remaining work, and prioritize the highest-value path to `done_when`.
- At exhaustion, do not start new implementation work. Update working memory, preserve the repository in a coherent state, run only validation that clearly fits in the remaining time, and report incomplete criteria honestly.
- Never infer an extension from user silence, prior runs, or remaining context. An extension requires the configured approval policy.
- A prompt-level budget is an agent behavior contract, not an operating-system kill switch. Use an external supervisor when hard process termination is required.

## Optional Budget Dimensions

Include only dimensions the user actually needs:

```xml
<budget>
max_elapsed_time: 5h
max_cost: none
max_iterations: none
max_parallel_jobs: 2
warning_at: 4h30m
check_interval: 15m
on_warning: checkpoint_and_prioritize
on_exhaustion: checkpoint_and_stop
extension_policy: explicit_user_approval
</budget>
```

Supported dimensions:

- `max_elapsed_time`: overall wall-clock ceiling
- `max_cost`: currency-denominated external API or compute ceiling, with the currency stated
- `max_iterations`: maximum meaningful implementation or experiment loops
- `max_parallel_jobs`: concurrency ceiling

Use `none` only when the user has explicitly accepted no limit for that dimension. Omit irrelevant dimensions rather than filling the block with ceremony.

## Interview and Tightening Rules

Clarify these points when they change product intent:

- whether the budget is a hard ceiling or a warning-only target
- what should happen near exhaustion
- whether an extension may be requested and who can approve it
- whether final verification must fit inside the budget
- whether paused time counts toward elapsed time

Default recommendations when the user only supplies a hard time limit:

- treat it as wall-clock elapsed time
- warn at 90 percent of the limit
- check every 15 minutes and at phase boundaries
- checkpoint and stop at exhaustion
- require explicit user approval for extensions
- keep final verification inside the same budget

## CONTROL.md Interaction

`GOAL.md` is the authoritative maximum. `CONTROL.md` may pause work, tighten the remaining budget, or request an extension, but it must not silently raise the maximum. If the user approves an extension, record the old limit, new limit, approver, and approval time in `PLAN.md` before continuing.
