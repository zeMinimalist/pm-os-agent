# Context Engineering & Memory: Cortex PM Chief-of-Staff Agent

> Module 4 · Context Engineering & Memory
>
> ✅ **What this validates:** The agent reasons on the right, safe inputs. This plan defines the context budget, per-source retrieve-vs-long-context decisions, retrieval-quality controls, and governed memory with explicit risk mitigations.
>
> Builds on the M1 agent line, M2 loop, and M3 orchestration map. Passing validation advances work only to the **HITL Stop**; nothing is published automatically.

## 1. Context budget

Cortex should prioritize context in this order:

1. **Complete task brief** — defines the requested outcome, project, and requirement that nothing advances beyond the **HITL Stop** without human review.
2. **Current team norms and agent-line safeguards** — preserves confidentiality, evidence standards, queue limits, and prohibited actions.
3. **Project record and current activity** — supplies the current status, metrics, PRs, issues, and sprint.
4. **Relevant roadmap section** — establishes scope, launch constraints, and confidentiality.
5. **Relevant past updates** — provides format and precedent but never overrides current evidence.
6. **Conversation history and rejected drafts** — compress, isolate, or discard first when the context becomes crowded.

The working context should contain only the current task, applicable rules, and evidence for the requested project. Unrelated projects and confidential roadmap sections should remain outside the drafting context. Long tool outputs and prior attempts should be compressed without removing source identifiers, figures, or human-review requirements.

## 2. Retrieve vs. long-context: per source

For each data source, Cortex either **retrieves** a relevant slice from a large, changing, or sensitive corpus or uses **long-context** to reason over one small, bounded source.

| Source | Size / volatility | Decision | Deciding factor | Why |
|---|---|---|---|---|
| Recent activity — `get_activity` | Large, continuously growing, highly volatile | **Retrieve** | Volatility | Retrieve the requested project’s current slice, then include the complete returned slice so metrics, PRs, and issues remain traceable. The brain-off probe showed that removing it caused Cortex to reuse stale 37% → 39% data instead of the current 41% → 43%. |
| Past updates — `search_past_updates` | Unbounded archive that grows weekly | **Retrieve** | Corpus size | Retrieve only relevant project precedent instead of including the entire history. Historical figures provide format and context but never override current activity. |
| Roadmap — `get_roadmap` | Medium-sized, changing, and contains confidential projects | **Retrieve** | Citation and audit | Retrieve the requested project’s relevant section so roadmap claims can be traced precisely and unrelated confidential projects do not enter the drafting context. |
| Team norms — `get_norms` | Medium-sized and updated over time | **Retrieve** | Volatility | Retrieve the current rules applicable to the task. Stale norms could incorrectly authorize publication, mishandle confidential data, or bypass the **HITL Stop**. |
| Task brief — `get_task` | One small, bounded, static document | **Long-context** | Corpus size and boundedness | Include the brief whole so Cortex sees every requirement, especially the requested project and the instruction that nothing goes out before human review. |

**Design principle:** Retrieval narrows large, changing, or sensitive sources. Long-context supports complete reasoning over one small, bounded source.

## 3. Retrieval quality plan

| Source | Routing | Document grading | Reranking | Self-verification | Caching |
|---|:---:|:---:|:---:|:---:|:---:|
| Recent activity — `get_activity` | ✓ | ✓ | — | ✓ | — |
| Past updates — `search_past_updates` | ✓ | ✓ | ✓ | ✓ | — |
| Roadmap — `get_roadmap` | ✓ | ✓ | — | ✓ | — |
| Team norms — `get_norms` | ✓ | — | — | ✓ | ✓ |
| Task brief — `get_task` | Long-context; retrieval moves not applicable | — | — | — | — |

### Recent activity

- Route using the exact requested project ID.
- Reject results belonging to another project or lacking source identifiers.
- Before the **HITL Stop**, confirm that every metric, date, PR, and issue in the draft matches the retrieved activity.
- Do not cache because activity changes frequently.

**Failure prevented:** Wrong-project evidence, stale metrics, and invented progress.

### Past updates

- Route by project and relevant update type.
- Remove unrelated-project results.
- Rank the newest relevant update first.
- Verify that historical facts are used only as precedent or formatting guidance—not presented as current evidence.
- Do not cache across source updates.

**Failure prevented:** Old figures, such as 37% → 39%, being presented as the current 41% → 43% result.

### Roadmap

- Route using the requested project ID.
- Remove unrelated sections from the drafting context.
- Exclude confidential or embargoed information from broader updates.
- Before the **HITL Stop**, verify every roadmap claim against the selected section and confirm that unrelated projects such as Pulsar and Orbit are absent.

**Failure prevented:** Wrong-project claims and confidential information leakage.

### Team norms

- Route to the policy sections applicable to the current task.
- Verify that the draft and proposed action comply with the retrieved rules, especially confidentiality, queue limits, and human approval.
- Cache only within the current run or under a short TTL tied to the source version.
- Invalidate the cache whenever the norms file changes.

**Failure prevented:** Applying an outdated rule or allowing work to bypass the **HITL Stop**.

### Task brief

Include the entire task brief as long-context. Do not fragment it through retrieval or compress away user constraints.

**Failure prevented:** Losing the requested project, deliverable, or instruction that nothing should be published without human review.

## 4. Memory map (your PM brain)

| Memory type | What Cortex stores | Scope and access | Lifetime / TTL |
|---|---|---|---|
| **Working** | Current task, selected evidence, current draft, proposed-story queue status, revision count, and critic verdict | Current run only; available to Cortex and selectively passed to the critic | Delete when the run reaches success, safe handoff, or escalation. Preserve only the final review artifact at the **HITL Stop**. |
| **Episodic** | Project ID, task ID, terminal outcome, escalation reason, and the PM’s final decision | Scoped to the same project; available only when relevant to a future run | 30 days, then delete or deliberately promote a still-valid fact to semantic memory |
| **Semantic** | Stable project identity, PM-approved communication preferences, and references to authoritative sources | Long-lived but write-gated; no automatic updates from tasks, drafts, or critic output | Review every 90 days; refresh or delete anything that cannot be verified |
| **Shared** | Task and project ID, selected source evidence, current draft and stories, queue status, revision number, critic verdict, reasons, and required corrections | Shared only among Cortex, the critic, and the PM review artifact | Retain through the active run and **HITL Stop**; delete after the PM acts or after seven days without action |

### What must remain isolated

- Cortex’s internal drafting scratch
- Superseded drafts and reasoning
- Unrelated-project data
- Confidential evidence that is unnecessary for the current task
- Credentials, API keys, and unnecessary personal information

### Promotion rules

- Passing critic validation does not create durable memory.
- The PM’s action after the **HITL Stop** is the authoritative outcome.
- Only stable, validated, PM-approved facts may move from episodic to semantic memory.
- Mutable facts—status, metrics, issue severity, and launch dates—must be retrieved fresh rather than promoted.

## 5. Memory risks & mitigations

| Risk | Where it affects Cortex | Mitigation |
|---|---|---|
| **Drift** | A stored project fact or preference gradually diverges from its authoritative source | Store provenance with durable facts, revalidate them when used, review semantic memory every 90 days, and delete anything that cannot be verified |
| **Poisoning** | A misleading task, pasted note, prompt injection, bad retrieval, or rejected draft becomes trusted memory | Treat inputs as evidence rather than memory instructions; require validated provenance and PM approval for durable writes; quarantine suspicious inputs; preserve rollback history |
| **Staleness** | A formerly correct metric, status, severity, policy, or launch constraint is reused after it changes | Store retrieval time and source version, invalidate caches when sources change, apply explicit TTLs, and retrieve mutable operational facts every run |
| **PII and retention** | Personal or confidential information persists beyond the task or becomes accessible to unrelated agents and projects | Store no persistent PII by default; minimize and redact retained data; scope access by project and role; enforce deletion TTLs; support deletion and audit requests; never store credentials or API keys |

### Agent-line and bounds connection

Memory access follows the Module 1 agent line:

- Cortex may read current evidence and create temporary working context below the line.
- Cortex may not independently convert an unapproved claim into durable memory.
- Semantic-memory writes require PM approval after the **HITL Stop**.

The TTLs and access scopes are enforceable bounds for Module 5—not informal preferences. If Cortex cannot verify provenance, freshness, or permission, it must retrieve again, refuse, or escalate rather than rely on memory.
