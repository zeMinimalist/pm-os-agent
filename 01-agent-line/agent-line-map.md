# Agent Line Map: Cortex PM Chief-of-Staff Agent

> Module 1 · The Agent Line
>
> **What this validates:** Every risky action has a clear owner. This map defines above- and below-the-line decisions, HITL checkpoints, and scores for reversibility, blast radius, and measurability.

## The workflow, decision by decision

**Scoring:** H = High · M = Medium · L = Low

| Decision / action | Reversibility | Blast radius | Measurability | Above / Below | HITL? |
|---|---:|---:|---:|---|---|
| Pull project state and recent activity | H | L | H | Below | No |
| Decide which context is relevant | H | L | M | Below | Required: verify coverage before publication |
| Draft the status update | H | L | H | Below | No |
| Decide tone and commitment level | M | M | L | Above | Required: approve tone and commitments |
| Flag a project as at risk | H | L | H | Below | No |
| Choose what to escalate | M | H | L | Above | Required: approve escalation |
| Propose a capped story batch | H | L | H | Below | Required before creating backlog items |
| Approve and post an update company-wide | L | H | H | Above | Required: human approves and posts |

## Agent anatomy (sketch)

- **Model:** Use the configured `gpt-4o-mini` or another fast, cost-efficient model for routine retrieval, ranking, drafting, and self-checking. Escalate to a frontier model when evidence conflicts, an escalation recommendation carries material risk, or the draft repeatedly fails validation.
- **Tools:** `get_project` · `get_activity` · `search_past_updates` · `get_roadmap` · `get_norms` · `propose_stories`
- **Tool restriction:** `propose_stories` only queues a capped batch of proposals; it does not create backlog items.
- **Memory:** Retain the current task, project state, recent activity, source log, past updates, team norms, draft revisions, critic feedback, and pending human decisions. Do not publish or learn permanently from unapproved output.
- **Loop:** Placeholder — to be defined in Module 2.
- **Bounds:** Placeholder — to be formalized in Module 5.
- **Evals:** Placeholder — to be formalized in Module 5.

## The golden rule, applied

1. **Pull project state and activity** sits below the line because it is highly reversible, has a low blast radius, and is highly measurable; **deciding factor: all three axes are green**.
2. **Decide which context is relevant** sits below the line with HITL review because it is highly reversible and has a low blast radius, but relevance is only moderately measurable; **deciding factor: measurability**.
3. **Draft the status update** sits below the line because it is highly reversible, has a low blast radius, and is highly measurable against source evidence and required sections; **deciding factor: all three axes are green**.
4. **Decide tone and commitment level** sits above the line with HITL approval because it has medium reversibility, a medium blast radius, and low measurability; **deciding factor: measurability**.
5. **Flag a project as at risk** sits below the line because the internal flag is highly reversible, has a low blast radius, and is highly measurable against defined thresholds; **deciding factor: all three axes are green**.
6. **Choose what to escalate** sits above the line with HITL approval because it has medium reversibility, a high blast radius, and low measurability; **deciding factor: blast radius**.
7. **Propose a capped story batch** sits below the line because proposals are highly reversible, have a low blast radius, and are highly measurable against the brief and batch cap; **deciding factor: all three axes are green**.
8. **Approve and post an update company-wide** sits above the line with mandatory HITL because publication has low reversibility, a high blast radius, and high measurability; **deciding factor: blast radius**.

## Hardest call

My hardest call was deciding whether Cortex should independently select relevant context. I kept the work below the line but added HITL review because measurability settled the decision: source coverage can be checked, but relevance cannot be judged reliably enough for unsupervised use.
