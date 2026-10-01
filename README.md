# Cortex: A Bounded PM Chief-of-Staff Agent

> Cortex turns current project evidence into a grounded status update and review-only sprint stories, validates the work independently, and routes it to a HITL Stop—so PMs review instead of assemble.

_Simon Hartigan, Agentic Loops for PMs Cohort, September 2026_

Repo: https://github.com/zeMinimalist/pm-os-agent

This repo is my final project for the Agentic Loops for PMs Certification, **Cortex: A Bounded PM Chief-of-Staff Agent**. Each module’s artifact lives in its own folder; this README is the dashboard and the pitch.

---

## Module artifacts

### M1 · The Agent Line
- **Agent-line map**: [`01-agent-line/agent-line-map.md`](01-agent-line/agent-line-map.md)

### M2 · Loop Engineering
- **Loop spec**: [`02-loop-design/loop-spec.md`](02-loop-design/loop-spec.md)

### M3 · Orchestration &amp; Subagents
- **Orchestration map**: [`03-orchestration/orchestration-map.md`](03-orchestration/orchestration-map.md)

### M4 · Context Engineering &amp; Memory
- **Memory &amp; context plan**: [`04-memory-context/memory-and-context.md`](04-memory-context/memory-and-context.md)

### M5 · Bounds &amp; Evals
- **Bounds &amp; evals**: [`05-bounds-evals/bounds-and-evals.md`](05-bounds-evals/bounds-and-evals.md)

### M6 · Autonomy &amp; Production
- **Production &amp; autonomy plan**: [`06-autonomy/production-and-autonomy.md`](06-autonomy/production-and-autonomy.md)
- **Prototype write-up**: [`06-autonomy/prototype.md`](06-autonomy/prototype.md)

---

## Ship plan

### Autonomy dial (per segment)
- Product lead / regular operator → Assisted (Rung 2): Cortex retrieves, drafts, validates, and queues review-only proposals; the product lead publishes or creates backlog items after the HITL Stop.
- Engineering lead / occasional approver → Assisted (Rung 2): Cortex prepares technical summaries and story proposals; tracker actions, severity judgments, and commitments remain human decisions.
- Executive stakeholder / update consumer → Shadow (Rung 1): Cortex drafts behind the scenes; executives receive only human-approved updates.

The dial changes below-the-line checkpoints but never moves the agent line. Consequential actions retain an action-specific HITL Stop for every segment.

### Trust Ladder rung + eval gate
Current rung: Assisted (Rung 2). Cortex prepares and validates work, but humans perform consequential actions after the HITL Stop.

Gate to Supervised (Rung 3): EV-1 immediate injection refusal, EV-2 partial-side-effect reporting, and EV-6 Cortex–critic status-policy coordination must pass. Cortex must then complete at least 50 assisted-mode runs across 4 consecutive weeks with 100% safety-critical eval pass, 100% HITL compliance, ≥95% tool-call accuracy, ≥90% clean trajectories, ≥80% recovery success, and ≥90% task completion.

Clean incident record: 0 confidential leaks, forbidden actions, permission escalations, wrong-channel sends, bypassed HITL Stops, or unreported partial side effects. Any material incident activates containment, drops Cortex at least one rung, requires root-cause remediation, and resets the 4-week/50-run qualification window after the fix.

### Deployment plan
- Runtime: Serverless functions triggered by an authorized inbound hook, with a daily 09:00 backup schedule. Durable state stores task IDs, budgets, traces, approvals, and HITL Stop artifacts.
- Owner: Simon Hartigan is the prototype service owner. Real production rollout is blocked until named engineering and security backup owners are assigned.
- Escalation: Model, tool, or infrastructure failures go to engineering on-call; confidentiality or permission incidents go to security/privacy. If no owner responds within 15 minutes, activate the kill switch.
- Rollback: Stop triggers within 5 seconds, revoke JIT credentials, freeze pending actions, disable the affected tool, revert to the last known-good Git version, and drop the affected segment one rung.
- Monitoring: Eval pass rates, task completion, recovery, critic rejection, escalations, runtime, cost-to-serve, HITL Stop decisions, partial side effects, duplicate queues, and trust incidents.

### ROI metrics + widen-autonomy rule
- Outcome: ≥90% of eligible weekly-update tasks reach the HITL Stop with a complete artifact accepted with no more than minor edits over 4 weeks.
- Cost-to-serve: Fully loaded cost per accepted task—including models, tools, retries, infrastructure, monitoring, and human review—must be ≥50% below the manual baseline over 4 weeks.
- Trust: 0 material incidents, 0 bypassed HITL Stops, 0 unreported partial side effects, and ≤2 contained near-misses per 100 completed tasks.

Widen-autonomy rule: Raise Cortex by no more than one rung for one named segment and capability only after EV-1, EV-2, and EV-6 pass, all M5 thresholds hold for at least 50 runs and 4 consecutive weeks, cost-to-serve clears its target, and there are 0 material trust incidents. Any material incident immediately drops the affected segment at least one rung and resets the qualification window.

### Governance &amp; strategy
- Compliance: Only allowlisted, project-scoped, minimum-necessary data may enter Cortex. Credentials, regulated data, private HR/legal notes, unrelated confidential information, and unauthorized embargoed roadmap data are prohibited. A HITL Stop cannot override compliance policy.
- Safety: Company-wide publication, real backlog creation, commitments, launch-gate changes, ticket closure, escalation delivery, and durable-memory promotion remain above the agent line. Each requires an action-specific HITL Stop. A kill switch stops runs, revokes credentials, freezes queued actions, and preserves traces.
- Reliability: Maximum 8 iterations, 2 critic revisions, 90 seconds, $0.05/run, and $0.50/day. Retry transient failures once; never substitute projects or present stale evidence as current. Missing or conflicting evidence produces a held artifact and exception-path HITL Stop.
- Forward strategy: The next candidate is exact-payload internal status delivery for the product-lead segment after human approval. It requires single-use JIT credentials, payload-bound approval, 100% destination matching, 0 duplicate or unauthorized sends, and the full 4-week/50-run gate.

---

## Build insights

- **Friction point.** The biggest friction was Cortex–critic coordination: the validator applied the same status policy inconsistently, forcing grounded drafts through unnecessary revisions and into the exception-path HITL Stop. I learned that a critic needs deterministic rules, calibration, and its own evals.
- **Key learning.** Safety cannot live only in the prompt. Iteration, time, cost, permission, and publication limits need infrastructure enforcement, while the HITL Stop gives the operator a visible, reviewable decision point.
- **Aha moment.** Autonomy is not one switch for the whole product. It is a dial set per user segment and capability, raised only after measurable eval performance and a clean incident window; the agent line and consequential HITL Stops remain fixed.

---

_Certification submission, Agentic Loops for PMs Certification._
