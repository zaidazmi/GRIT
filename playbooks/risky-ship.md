# Risky Ship Playbook

Open this playbook only when at least one visible trigger applies:

- Authentication or permissions.
- Payments.
- Sensitive user data.
- Database migrations.
- Destructive actions.
- Public APIs or contracts other systems depend on.
- Changes that are difficult to undo.

Otherwise use [Quick Ship](quick-ship.md). If uncertainty is the only concern, investigate in Quick Ship or run a disposable Spike; switch here if that investigation reveals a trigger. When a trigger applies, also read [GRIT.md](../GRIT.md), [Verification](../reference/verification.md), and the relevant parts of [Agent Operations](../reference/agent-operations.md).

Risky Ship adds evidence and human judgment, not paperwork for its own sake. Collapse artifacts when that improves clarity; separate them when different owners or agents need different truths.

## Minimum bar

- Human-owned intent and success criteria.
- A reviewed delivery contract with measurable non-functional requirements.
- A technical plan and independently verifiable task slices.
- Protected acceptance evidence and preservation tests.
- Explicit permissions and human gates.
- Adversarial review, security review, rollout, rollback, and observation.

## 1. Challenge the premise

```text
PROBLEM: [What problem does this solve, for whom, and how is it handled today?]
EVIDENCE: [What observation, research, or data says this is worth solving?]
SMALLEST: [What is the smallest version that delivers or tests value?]
EXISTS: [Does the capability already exist under another name?]
NULL CASE: [What happens if nothing is built?]
SUCCESS OR KILL: [What outcome causes continuation, rollback, or abandonment?]
```

Cheap prototypes change the order of discovery, not the need for judgment. A prototype is a disposable learning artifact. It does not graduate into production without a delivery contract and the full shipping loop.

## 2. Build the artifact stack

### Discovery brief

The discovery brief owns why the work should exist:

- Target user or affected system.
- Evidence and current workaround.
- Desired outcome and success metric.
- Riskiest assumptions across value, usability, feasibility, viability, security, and ethics.
- Experiment or prototype.
- Continue, change, or kill decision.

For durable artifacts, add `STATUS`, `OWNER`, `DATE/REVISION`, and `SUPERSEDES` so a future reader or agent can tell whether the document still governs the work.

### Delivery contract

The delivery contract owns what must be true:

```text
INTENT:
USER OR SYSTEM VALUE:
CURRENT BEHAVIOR:
NEW BEHAVIOR:
INVARIANTS / WHAT MUST STAY THE SAME:
SUCCESS SCENARIOS:
FAILURE AND EDGE SCENARIOS:
NON-GOALS:
CONNECTIONS AND AFFECTED WORKFLOWS:
PERMISSIONS AND HUMAN GATES:
MEASURABLE NFRS:
ACCEPTANCE EVIDENCE:
ROLLOUT:
ROLLBACK:
OBSERVATION WINDOW AND THRESHOLD:
UNCLEAR OR ASSUMED:
```

The contract is authoritative for the current pass, not eternal. If implementation or user learning changes what “right” means, update the contract and evidence before the code.

### Technical plan and task graph

The plan owns how the contract will be delivered:

- Existing patterns and interfaces to reuse.
- Proposed architecture and boundaries.
- Data, API, permission, and operational changes.
- Deployment and migration ordering.
- Dependency graph and parallel-safe work.
- Independently implementable and verifiable vertical slices.
- Alternatives rejected and why.

Do not mix unresolved product intent into an implementation plan. Mark it as a decision that needs an owner.

### Decision record

When several approaches are genuinely viable, make the tradeoff inspectable instead of letting the implementation agent choose implicitly:

```text
DECISION:
OWNER AND DATE:
CONTEXT AND EVIDENCE:
OPTIONS CONSIDERED:
CRITERIA AND RELATIVE WEIGHT:
CHOICE AND RATIONALE:
ASSUMPTIONS:
RISKS AND MITIGATIONS:
REOPEN IF:
SUPERSEDES:
```

For consequential multi-option choices, score the options against the agreed criteria and vary the weights to test whether the result is stable. The matrix exposes tradeoffs and sensitivity; it informs the accountable human and does not replace judgment.

### Proof map

Give important requirements stable IDs and map them to independent evidence.

| ID | Requirement | Verification | Evidence owner |
| --- | --- | --- | --- |
| AC-1 | Duplicate payment requests do not double-charge | Integration test plus idempotency inspection | Reviewer |
| AC-2 | p95 latency remains below the agreed budget | Load test | CI or performance owner |
| AC-3 | Existing refunds remain unchanged | Preservation suite | Independent reviewer |

Use Given/When/Then or constrained requirement syntax only when it removes real ambiguity. Do not manufacture ceremonial user stories.

## 3. Run the system gap scan and hole test

Before implementation, inspect the change across the dimensions that apply:

| Dimension | Questions |
| --- | --- |
| Domain and data | What entities, relationships, invariants, retention, and privacy rules change? |
| Actors and authority | Who or what may read, decide, write, approve, retry, or override? |
| Topology and trust | Where do data and actions cross processes, services, devices, vendors, or trust boundaries? |
| Events and time | What triggers the behavior, in what order, with which states, deadlines, retries, and terminal conditions? |
| Rules and recovery | What must always or never happen, how can it fail, and how is it detected, repaired, or rolled back? |
| Interfaces and dependencies | Which contracts, consumers, providers, versions, and operational owners are affected? |

Undefined intersections are questions to resolve or explicitly assign—not freedom for an agent to invent behavior. Use only the dimensions relevant to the change; the scan is a gap detector, not a requirement for more documents.

Before implementation, give intent and expectations to a fresh reviewer and ask where an implementer would still have to guess. Close guesses that could change the outcome.

For multiple services, state transitions, or non-trivial data flow, create a text-based diagram as a diagnostic:

| Situation | Diagram | Reveals |
| --- | --- | --- |
| Multi-step workflow | State machine | Invalid transitions and missing terminal states |
| API integration | Sequence diagram | Ownership, ordering, and error paths |
| Data model change | Entity relationship | Broken references and constraints |
| Service interaction | Flowchart | Ambiguous branches and circular dependencies |

Skip diagrams when the structure is already obvious.

## 4. Protect the proof

- Run and record the baseline before editing.
- Review acceptance tests before implementation.
- Do not allow the implementation agent to weaken, delete, or redefine protected evidence without approval.
- Add preservation tests for behavior that must remain unchanged.
- Use a fresh reviewer to generate adversarial, boundary, concurrency, and abuse cases.
- Consider hidden tests or mutation testing where specification gaming would be costly.
- For non-deterministic systems, use versioned eval datasets, repeated trials, calibrated graders, and trace inspection.

Passing visible tests is not sufficient when the agent controls both the solution and the tests.

## 5. Keep humans at judgment points

Presence is more valuable than a ceremonial approval on a diff too large to understand. Require human involvement at:

- Intent and success criteria.
- Consequential unresolved decisions.
- First implementation checkpoint.
- Irreversible or high-blast-radius actions.
- Final product, domain, and rollout judgment.

```text
Before changing data models, auth, billing, permissions, migrations,
public contracts, or production state, stop and show:
- Intended change
- Affected connections and users
- Permission required
- Verification plan
- Rollout and rollback path
```

## 6. Specify and verify non-functional requirements

Functional success does not prove production readiness. Put measurable requirements in the delivery contract before implementation, covering the applicable reliability, security, privacy, performance, accessibility, cost, observability, and recovery risks. Use the authoritative [Verification hardening checklist](../reference/verification.md#hardening) to design and verify them; do not maintain a second checklist here.

## 7. Handle migrations as a rollout

Plan database changes as `expand -> migrate/backfill -> contract` so old and new application versions can coexist.

- Sequence schema and application deployments explicitly.
- Prefer backward-compatible additions for populated tables.
- Validate database-specific locking and table-rewrite behavior.
- Create indexes concurrently or online where supported and appropriate.
- Backfill large datasets in bounded, observable batches outside the schema transaction.
- Give one owner authority over migration ordering; parallel branches must not create competing histories.
- Test forward migration, compatibility during rollout, and the realistic rollback or roll-forward path.

## 8. Review, release, and observe

Use a fresh, adversarial reviewer. Model diversity may help, but independent evidence and reasoning strength matter more.

Before release:

- [ ] Every proof-map item has current evidence.
- [ ] Full relevant regression, type, lint, static, and security checks pass.
- [ ] The running system was exercised through its critical path.
- [ ] Permission and data-flow changes were reviewed.
- [ ] Residual risks and rejected alternatives are recorded.
- [ ] Rollout owner, stages, rollback mechanism, and stop threshold are explicit.

After release, observe the defined metrics for the defined window. Do not call the change successful merely because it matches the contract: software correctness and product success are different claims. Feed incidents, support cases, and unexpected behavior back into the contract, eval set, or project constraints.

## Evidence record

Preserve:

- Contract and plan revision.
- Model or harness version when relevant.
- Baseline and commands run.
- Tests, evals, screenshots, logs, metrics, and traces.
- Permissions approved or denied.
- Human interventions and decisions.
- Diff or release identifier.
- Residual risks, rollout state, observation results, and rollback path.
