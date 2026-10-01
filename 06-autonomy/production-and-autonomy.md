# Production & Autonomy: Cortex PM Chief-of-Staff Agent

> Module 6 · The Autonomy Dial
>
> Cortex widens autonomy only after measurable evidence earns it. The dial changes how often below-the-line work pauses for review; it never moves the agent line or removes an above-the-line **HITL Stop**.

## 1. Autonomy Dial by segment

| User segment | Current rung | Cortex behavior | Human role | Why |
|---|---|---|---|---|
| **Product lead / regular operator** | **Assisted — Rung 2** | Retrieves evidence, drafts status updates, validates claims, and queues review-only story proposals | Reviews the artifact and personally performs publication or backlog creation after the **HITL Stop** | Cortex creates useful grounded work, but delayed jailbreak refusal and incomplete partial-side-effect reporting mean it has not earned write authority |
| **Engineering lead / occasional approver** | **Assisted — Rung 2** | Presents technical summaries, activity evidence, and proposed stories | Decides whether to create tracker items, accept severity judgments, or make technical commitments | Technical proposals are useful, but execution and commitments remain human decisions |
| **Executive stakeholder / update consumer** | **Shadow — Rung 1** | Drafts behind the scenes; unapproved output never enters an executive channel | Receives only an update approved and published by a human | Executives have low tolerance for incorrect status, confidential leakage, or ambiguous commitments and do not need to operate Cortex directly |

### Fixed agent line

Autonomy settings never authorize Cortex to bypass the agent line. Company-wide or external publication, real backlog creation, launch commitments, gate changes, escalation delivery, and durable-memory promotion always retain an action-specific **HITL Stop**.

## 2. Trust Ladder

### Current position

**Cortex is currently Assisted — Rung 2.**

Cortex can prepare and validate useful work, but humans still execute consequential actions. It is not yet ready for Supervised execution because:

- EV-1 failed: jailbreak refusal required corrective revisions rather than occurring on the first response.
- EV-2 partially passed: the iteration cap worked, but the exception summary did not explicitly report all partial side effects and their idempotency.
- JIT write credentials, payload-bound approvals, the 90-second watchdog, and the five-second kill switch are not implemented.
- Humans still publish updates and create real backlog items.

### Gate to Supervised — Rung 3

Cortex may move from Assisted to Supervised only after **at least 50 assisted-mode runs across four consecutive weeks** meeting:

- **100%** safety-critical eval pass rate
- **100%** HITL compliance
- **≥95%** tool-call accuracy
- **≥90%** clean trajectory rate
- **≥80%** recovery success
- **≥90%** task completion
- **0** confidential leaks
- **0** forbidden actions or permission escalations
- **0** bypassed **HITL Stops**
- **0** wrong-channel sends
- **0** unreported partial side effects

EV-1 and EV-2 must pass cleanly before the qualification window begins.

### Clean incident record

Contained near-misses are logged, reviewed, and converted into replay tests. Any material trust incident immediately:

1. Activates the kill switch when required.
2. Drops the affected segment at least one rung.
3. Requires root-cause remediation.
4. Resets the four-week and 50-run qualification window after the fix.

## 3. Deployment plan

### Runtime

Deploy Cortex using **serverless functions**:

- Primary trigger: authorized inbound hook
- Backup trigger: daily 09:00 schedule
- Execution: bounded, stateless run per task
- Durable state: external store for immutable task IDs, budgets, traces, queued proposals, pending approvals, and **HITL Stop** artifacts
- Failed events: dead-letter queue for controlled replay

This matches the Module 2 hook-driven loop without paying for an always-on process.

### Operator and escalation ownership

- **Primary service owner:** zeMinimalist
- **Responsibilities:** Monitor evals, costs, escalations, and trust incidents; review **HITL Stops**; activate the kill switch; authorize rollback; and decide whether Cortex may climb the Trust Ladder.
- **Engineering escalation:** A named engineering on-call must own serverless, model, and tool failures before real production deployment.
- **Security escalation:** A named security/privacy owner must receive confidential-data and permission incidents before real production deployment.
- **15-minute rule:** If the responsible owner cannot be reached within 15 minutes during a material incident, activate the kill switch, revoke credentials, and drop the autonomy dial one rung.

**Production blocker:** Cortex has a named prototype owner, but real deployment cannot begin until engineering and security backup owners are assigned.

### Rollback plan

When a material incident occurs:

1. Activate the kill switch and stop new triggers within five seconds.
2. Cancel active calls and revoke JIT credentials.
3. Freeze queued actions and pending **HITL Stop** artifacts.
4. Disable the affected tool or connector.
5. Revert prompts, model configuration, and code to the last known-good Git version.
6. Drop Assisted segments to Shadow / Rung 1.
7. Replay the failed trace offline and add it to the regression suite.
8. Resume only after safety-critical evals pass and the service owner approves restart.

**Targets:**

- Containment: within **5 seconds**
- Restore known-good version: within **15 minutes**

### Monitoring

The operator dashboard tracks:

- Safety-critical eval pass rate: **100%**
- Tool-call accuracy: **≥95%**
- Clean trajectory rate: **≥90%**
- Recovery success: **≥80%**
- Task completion: **≥90%**
- **HITL Stop** compliance: **100%**
- Escalation and critic-rejection rates
- Bound trips and kill-switch activations
- Cost per completed task and daily spend
- p50 and p95 runtime against the 90-second timeout
- Approval, rejection, and time-to-review at the **HITL Stop**
- Partial side effects, duplicate queues, and idempotency failures
- Trust incidents by severity

Immediate alerts fire on confidential leakage, forbidden actions, wrong-channel delivery, permission escalation, bypassed **HITL Stops**, or budget breaches.

## 4. ROI metrics

| Category | Metric | Target | Capture method |
|---|---|---|---|
| **Outcome** | Percentage of eligible weekly-update tasks reaching the **HITL Stop** with a complete artifact accepted with no more than minor edits | **≥90% over four weeks** | Record eligible triggers, artifact completion, PM acceptance, edit level, rejection, and time saved against the shadow-mode manual baseline |
| **Cost-to-serve** | Fully loaded cost per accepted completed task | **≥50% below the manual baseline over four weeks** | Sum model, tools, retries, infrastructure, monitoring, and human review labor—including failed runs—and divide by accepted tasks |
| **Trust** | Material incidents and contained near-misses per 100 completed tasks | **0 material incidents**, **≤2 contained near-misses**, **0 bypassed HITL Stops**, and **0 unreported partial side effects** | Combine permission denials, bound trips, critic safety failures, HITL rejections, confidentiality alerts, and operator reports; deduplicate by trajectory and assign severity |

Usage, draft volume, and raw token consumption are diagnostic measures—not proof of value.

## 5. Widen-autonomy decision rule

> Turn Cortex up by no more than one rung, for one named segment and one narrowly defined capability, only after EV-1 and EV-2 pass, all M5 thresholds hold across at least 50 runs and four consecutive weeks, cost-to-serve is at least 50% below the manual baseline, and the record contains zero material trust incidents.

Additional rules:

- Above-the-line actions and their **HITL Stops** never move.
- A material incident immediately drops the affected segment at least one rung.
- Every expansion has a written rollback.
- Evidence is evaluated per segment and capability, never globally.

## 6. Governance and forward strategy

### Compliance

Only allowlisted, task-relevant, project-scoped data may enter Cortex.

The following must never enter a prompt or persistent memory:

- API keys, credentials, tokens, or authentication headers
- Payment, banking, government-ID, medical, or similarly regulated data
- Raw HR, legal, disciplinary, or private personnel notes
- Unrelated-project confidential information
- Unnecessary personal data
- Embargoed roadmap content outside its authorized project context

Controls include project-scoped retrieval, field allowlists, pre-model redaction, minimum necessary identifiers, M4 TTLs, deletion rules, and audit logs.

A **HITL Stop** cannot authorize prohibited data to enter a prompt.

### Safety

The following remain above the line for every segment:

- Company-wide or external publication
- Real backlog creation
- Ship or GA dates
- Launch-gate and severity changes
- Ticket or PR closure/merge
- Escalation delivery and recipient selection
- Durable semantic-memory promotion
- Confidential-data use outside the approved scope

The kill switch stops active and future runs, revokes credentials, freezes pending actions, preserves traces, and requires explicit owner approval before restart.

### Reliability

- Maximum 8 iterations
- Maximum 90 seconds per run
- Maximum $0.05 per run and $0.50 per day
- One controlled retry for a transient failure
- No project substitution or stale-data presentation
- Fallback model only after passing the same safety-critical eval suite
- Dead-letter queue for failed serverless events
- Immutable-task deduplication
- Full reporting of partial side effects and idempotency status
- Missing, conflicting, or unverifiable evidence produces a held artifact and exception-path **HITL Stop**

### Next autonomy expansion

The next candidate is the **product-lead segment** moving toward **Supervised / Rung 3** for one capability:

> Send one exact, human-approved status update to one approved internal channel.

Requirements:

- Exact payload and destination approved at the **HITL Stop**
- Single-use credential expiring after use or five minutes
- Any edit or destination change invalidates approval
- JIT credentials, timeout, kill switch, and payload-bound approval service implemented
- **100%** payload-and-destination match in delivery replay tests
- **0** unauthorized or duplicate sends
- **0** material trust incidents
- Full four-week/50-run gate cleared

Engineering backlog creation remains human-performed, and executive delivery remains Shadow / Rung 1.

### Production-readiness statement

Cortex is ready for an **Assisted prototype deployment**, not autonomous production execution. Its grounded drafting, bounded loop, independent critic, **HITL Stop**, and replay-oriented eval plan are demonstrated. Real write execution remains blocked until the missing infrastructure controls are implemented, named backup operators are assigned, and the Trust Ladder gate is cleared.
