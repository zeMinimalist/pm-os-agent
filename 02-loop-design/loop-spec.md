# Loop Spec: Cortex PM Chief-of-Staff Agent

> Module 2 · Loop Engineering, ★ Deliverable 2
>
> **What this validates:** Cortex knows when to run, what completion means, and when to stop or return control to a human.
>
> This is the blueprint for how the work assigned below the Module 1 agent line runs.

## 1. Trigger & loop type

**Chosen type:** Hook with a bounded goal loop and cron backup.

An authorized inbound PM task fires a hook that starts Cortex. Inside the run, a bounded goal loop iterates until the independent critic validates the output or a detectable stop condition fires. A daily 09:00 cron sweep catches authorized tasks missed by the hook.

Cortex deduplicates tasks using an immutable message or task ID so repeated hook events cannot create duplicate drafts or story batches.

**Why this design:**

- A hook handles inbound tasks immediately.
- A bounded goal loop supports revision and self-correction.
- A cron sweep provides recovery for missed events.
- A heartbeat would create unnecessary runs when no work exists.
- Cron is unsuitable as the primary trigger because it could delay urgent tasks.
- A goal loop alone defines iteration and exit, not what initially starts the run.

## 2. Goal / definition of done

Cortex is responsible for producing a source-grounded PM status update and a capped batch of proposed sprint stories, then stopping before anything is published or created.

A run ends in one of two valid terminal states:

1. **Successful completion:** The independent critic approves the grounded draft, the proposed story batch contains no more than ten items, and everything is queued for human review.
2. **Safe human handoff:** Cortex cannot complete the task safely and returns a critic-approved escalation explaining the missing evidence, conflicting information, failed bound, or required human decision.

Cortex never publishes an update, creates backlog items, or makes dates and commitments automatically.

## 3. Stop conditions

| Condition | What it looks like | What happens |
|---|---|---|
| **Success** | The critic passes a source-grounded draft; proposed stories are supported by the PRD and remain within the ten-item cap; the output clearly states that it is queued for review. | Save the approved draft, queue it for human review, log the trace, and stop. Nothing is published or created. |
| **Stuck / give up** | No material progress across two revisions; the same tool fails three times; two revisions are rejected; eight total iterations are reached; or the cost cap is reached. | Halt the loop, preserve the last draft and trace, record the detectable bound that fired, and hand off to a human. |
| **Escalate to human** | Required project data is missing or contradictory; confidential information may be exposed; status or risk remains ambiguous after two revisions; the request requires a firm date, external commitment, publication, or unauthorized action; or the story batch exceeds ten items. | Produce an evidence-backed escalation, stop at the Module 1 HITL checkpoint, and wait for human direction. |

### Self-validation

An independent critic—not Cortex itself—must verify:

- Every factual claim is grounded in retrieved source data.
- Status and risk language follows shared team norms.
- Confidential or embargoed information is excluded.
- Dates and commitments are supported and authorized.
- Proposed stories are grounded in the PRD and remain within the queue cap.
- The final state says “queued for review” and confirms that nothing was published or created.

## 4. State

### Within each run

Cortex tracks:

- Immutable task or message ID
- Project ID and trigger source
- Retrieved evidence and source references
- Tool calls, results, and errors
- Current draft and prior revision
- Critic feedback
- Iteration, revision, queue, and cost counters
- HITL status and terminal outcome

This state lets Cortex measure progress, avoid repeating failed actions, and determine when a detectable stop condition has fired.

### Across runs

Cortex retains only:

- Task IDs and outcomes needed for deduplication
- Approved audit logs
- Approved templates and team norms

Unapproved draft content is purged after the configured retention period. Project context remains isolated so confidential information cannot leak between projects.

## 5. The five things a loop can lean on

| Component | For Cortex |
|---|---|
| **Work tree** | Not needed yet because each run handles one bounded PM task with a short sequence of dependent actions. Add isolated workspaces when Cortex manages parallel or multi-project work. |
| **Skills** | Not needed yet because the current workflow is narrow and encoded directly in its prompt and tools. Add reusable skills when repeated PM procedures need standardized instructions. |
| **Plugins / connectors** | No live external connectors are wired yet. Cortex currently reads local fixtures. Planned replacements include Jira for project and activity data, Google Drive for PRDs, and an approved inbound task channel without automatic sending. |
| **Subagents** | Use the independent critic as the only subagent for now. It validates grounding, policy compliance, confidentiality, and completion. Additional specialists will be considered in Module 3. |
| **State tracking** | Use bounded operational state for evidence, drafts, feedback, counters, HITL status, deduplication, and audit history. Avoid broad long-term memory until Module 4 defines its controls. |

## Link to live loop

`00-build/agent.py`
