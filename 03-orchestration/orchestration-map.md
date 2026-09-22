# Orchestration Map: Cortex PM Chief-of-Staff Agent

> Module 3 · Orchestration & Subagents, ★ Deliverable 3
>
> **What this validates:** Nothing advances unchecked. Cortex uses the smallest justified team, with an independent validator, explicit hand-offs, bounded revisions, and a human review checkpoint.
>
> Builds on the Module 2 Loop Spec. Every split must earn its coordination cost.

## 1. Why split? (or why not)

| Reason | Applies? | Why / why not |
|---|---|---|
| **Separation of concerns** | No | Cortex’s retrieval, drafting, and story-proposal tasks support one outcome and can remain in one agent. Splitting them would add coordination cost without a clear reliability benefit. |
| **Parallelism** | No | The current workflow handles one project with inexpensive data pulls, so parallel agents would not create meaningful latency savings. |
| **Independent validator** | Yes | A separate validator reduces shared blind spots and creates an evidence-based gate before the draft reaches the HITL Stop. |
| **Context-window pressure** | No | The task and evidence fit within one focused context; observed failures came from inconsistent evaluation rather than excessive context. |

**Decision:** Cortex remains one primary agent with exactly one validator subagent. The split is justified by independent judgment—not parallelism, context pressure, or architectural ambition.

## 2. Topology

**Pattern:** Single agent + validator subagent

```text
[Inbound PM task]
        ↓
[Cortex: retrieve evidence, draft update, propose stories]
        ↓
[Validator: independently check five rules]
        ├─ PASS → [HITL Stop: PM reviews queued draft]
        └─ FAIL → [Cortex revises] → [Validator]
                       └─ after 2 failed revisions
                          → [HITL Stop: held + escalated to PM]
```

## 3. Roster

| Agent / subagent | Responsibility | Runs which Loop Spec |
|---|---|---|
| **Cortex — PM Chief of Staff** | Read the inbound task, retrieve evidence, draft the update, and queue capped story proposals | Module 2 Loop Spec: hook-triggered, bounded goal loop |
| **Critic — Independent Validator** | Check Cortex’s draft against five explicit rules; pass, return for revision, or escalate after the cap | Validation loop: one review per draft, maximum two revisions |

Retrieval remains a tool capability rather than another agent because it requires no independent judgment.

## 4. Communication & hand-offs

Cortex and the critic use a plain structured hand-off inside the current Python process. MCP and A2A are not required for the current in-process design.

### Cortex → Critic

- Requested project ID
- Draft update and proposed stories
- Retrieved source evidence
- Queue status
- Revision number

### Critic → Cortex

- `pass` or `fail`
- Failed check names
- Evidence-based reasons
- Required corrections

### After validation

- **Pass:** Draft, evidence, and verdict advance to the PM’s HITL Stop.
- **Fail:** Reasons return to Cortex for revision.
- **Revision cap reached:** Last draft and validation history are held and escalated to the PM.

## 5. The validator

### What the critic checks

1. **Project identity:** The draft references the requested project and only real PR/issue IDs from retrieved data.
2. **Claim grounding:** Every number, date, progress claim, and risk statement traces to a source.
3. **Status policy:** The draft uses the recorded project status unless explicit evidence supports changing it; it never invents severity or impact.
4. **Story bounds:** Every proposed story traces to the PRD, and the batch contains no more than ten items.
5. **Agent-line compliance:** The draft exposes no confidential data, makes no unsupported commitment, publishes nothing, and advances only to the PM’s HITL Stop.

### Fail action

The critic blocks the failed draft, logs the failed checks, and returns specific reasons to Cortex for revision.

### Revision cap

Cortex may revise a failed draft no more than two times. If the second revised draft still fails, the critic blocks it and Cortex escalates the last draft, evidence, and validation history to the PM.

### Pass action

A passing draft advances to the HITL Stop for PM review. Passing never authorizes automatic publication or creation.

## 6. State: shared vs. isolated

### Shared

- Task and requested project ID
- Retrieved source evidence
- Draft and story proposals
- Queue status and revision count
- Validator verdict and failure reasons
- Artifacts presented to the PM at the HITL Stop

### Isolated

- Cortex’s internal drafting context
- Critic’s independent evaluation context
- Unrelated project data
- Prior rejected reasoning that could bias a fresh review

Selective sharing gives the critic enough evidence to validate claims without inheriting Cortex’s assumptions.

## 7. Cost & latency budget

The validator adds one model call per draft. A normal pass adds one call; the worst case adds three validator calls across the initial draft and two revisions.

Target no more than **$0.01** in total validation cost and **30 seconds** of added latency per run. After two failed revisions, stop and escalate to the PM rather than delaying the HITL Stop further.

## Evidence

The required critic-rejection transcript is saved in `06-autonomy/prototype.md`. It shows the critic rejecting an unsupported claim, blocking the draft, returning revision feedback, and escalating after the two-revision cap.
