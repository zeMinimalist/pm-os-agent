# Prototype: Cortex PM Chief-of-Staff Agent

> Module 6 · ★ Deliverable 1, the working agent demo
>
> ✅ **What this validates:** the agent actually runs end to end, by the end you'll have proven it with real screenshots of your Cortex across the six required moments (M2 to M6).

## What it does

_One paragraph: the agent in action, end to end._

## How you built it

- **Coding agent:** _which one you directed (Claude Code / Cursor / Codex)_
- **Model + bounds:** _model used, max iterations, cost cap, queue cap_
- **Repo / config:** _path to your build in `00-build/`_
- **Live link:** _[shareable URL, optional bonus]_

## Screenshots (required, collected M2 to M6)

Real screenshots of *your* Cortex running. These are the `00-build/CORTEX-ANATOMY.md` set and they are required, a link alone is not enough.

| # | Screenshot | What it shows | From |
|---|---|---|---|
| 1 | _[img]_ | happy-path run: a real drafted update + the HITL checkpoint (queued, not posted) | M2 |
| 2 | _[img]_ | the critic rejecting a bad draft (revise/block) | M3 |
| 3 | _[img]_ | a grounded update citing pulled activity + a caught hallucination | M4 |
| 4 | _[img]_ | jailbreak refused + escalated | M5 |
| 5 | _[img]_ | an iteration/cost/queue bound halting a runaway | M5 |
| 6 | _[img]_ | end-to-end run | M6 |

## How to run it

_Minimal steps for someone to reproduce the demo (env vars, and the command or the coding-agent prompt you used)._

## Module 2 — Loop stop proof

![Cortex safe human handoff after missing project data](m2-safe-handoff.png)

## Module 3 — Independent critic rejection

> Cortex’s independent validator rejected an unsupported claim, blocked the draft before the HITL Stop, allowed two revisions, and escalated after the revision cap.

```text
CORTEX DRAFT:
“The open issue regarding empty-state copy is under review but does not
impact the overall project health or prevent progress.”

CRITIC:
{
  "verdict": "fail",
  "reasons": [
    "The output mentions that the open issue does not impact the overall
    project health and does not prevent progress, which is not justified
    by the pulled data."
  ]
}

-> critic rejected; revision 1/2
-> critic rejected; revision 2/2

REVISION CAP hit (2). Escalating to a human instead of looping.

LAST DRAFT:
Held, NOT posted, escalated to a human.
```

## Module 4 — Grounding and withheld-source probe

![Grounded Cortex run](m4-grounded.png)

> **Grounded state:** With current activity available, Cortex cited PRs #820/#823 and activation moving from 41% to 43%. The critic passed the draft, which advanced only to the HITL Stop and was not posted.

![Withheld-source Cortex run](m4-withheld-source.png)

> **Withheld-source state:** With `get_activity` unavailable, Cortex reused stale 39% to 41% evidence. The critic failed the draft, the revision cap fired, and the draft was held and escalated rather than posted.
