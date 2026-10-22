---
name: hk-execution
description: >-
  Use for planning and executing long or multi-step tasks, projects, automations, and anything with many tool calls. Borrows Hong Kong throughput habits (parallel work, quick pivots, orchestration, Rail-plus-Property incentive design) and guards against overwork-style failure modes such as thrash, hurry sickness, skipped steps and optimistic plans.
license: MIT
metadata:
  author: kamingfung
  version: "1.0"
  repository: https://github.com/kamingfung/hk-agent-skills
---

# hk-execution — Throughput Without Burnout

HK runs at speed, but long hours past about 45 per week lower output and hide planning problems (W1, W5, W49). An agent's equivalent is a context window full of thrash. Aim for MTR cadence, not overtime.

## Borrow
- **Plan the route, then run on schedule.** Spend a short plan up front, then execute the steps in order (S31, S67).
- **Parallelize independent work.** Run searches, reads and subagents at the same time. Keep a frequent service and never make anyone wait for a single bus (S3, S77).
- **Pivot fast.** If an approach fails twice, change approach rather than retrying harder. Plastic factories switched product lines in weeks (S22, S23, S91).
- **Align incentives.** Shape the task so the easy path is also the correct one: tests that guard the goal, defaults that are safe (S31, S68).
- **Prepare before the signal.** Gather context, credentials and the checks you'll need before you start, not halfway through (S46–S48).

## Guard
- **Premortem (30 seconds).** Ask "If this failed, why?" and mitigate the top one or two risks before committing (W50, W95).
- **No hurry sickness.** Steady and deliberate beats frantic: slow is smooth, smooth is fast (W91, W92).
- **Checkpoints.** After each milestone, state what's done, what's verified and what's next. If you're off plan, re-plan. Don't just push harder (W49).
- **Don't skip routine steps under pressure.** Use the sign-in, time-out and sign-out checklist (W93, W94).
- **Stop the line.** If an unexpected state or contradictory instruction appears, pause and surface it. Momentum is not a reason to continue (W98, W69).
- **Leave a trail.** Write a handoff note covering decisions, open questions and unverified items, so the next session doesn't start cold (W100).
- **Reflect once.** After a correction, write the lesson in one sentence and apply it for the rest of the task (W99).

Source IDs (`S#`, `W#`) resolve in [references/sources.md](references/sources.md).
