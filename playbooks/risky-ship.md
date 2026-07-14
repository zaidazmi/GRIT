# Risky Ship Playbook

Risky Ship extends the [Quick Ship](quick-ship.md) delivery loop. Add it only when the change touches:

- Authentication, permissions, security boundaries, or agents taking consequential actions.
- Payments.
- Sensitive user data.
- Database migrations.
- Destructive actions.
- Public APIs or contracts other systems depend on.
- Changes that are difficult to undo.

If none applies, stay in Quick Ship. When one applies, also load [Verification](../reference/verification.md) and the relevant parts of [Agent Operations](../reference/agent-operations.md).

Risky Ship adds evidence and human judgment, not paperwork. Keep one working document by default. Split sections only when they have different owners or must be maintained separately.

The PM or founder owns intent, risk tolerance, and release judgment. The agent or engineer proposes and completes the technical sections.

Every Risky Ship change requires final review by a human with relevant technical or domain competence.

## Minimum bar

- Human-owned intent and success criteria.
- A reviewed Build Contract with measurable non-functional requirements.
- A technical plan with independently verifiable slices.
- Protected acceptance evidence and preservation tests.
- Explicit permissions and human gates.
- Adversarial review and applicable security review.
- Staged rollout, rollback, and observation.

## 1. Extend the Build Contract

Start with the [Quick Ship Build Contract](quick-ship.md#1-agree-the-build-contract), then add the fields that apply:

```text
SUCCESS OR KILL: Which outcome causes continuation, rollback, or abandonment?
PERMISSIONS AND HUMAN GATES:
QUALITY LIMITS: Reliability, security, privacy, performance, accessibility, cost, observability, recovery.
ROLLOUT: Stages, owner, entry/exit criteria, and stop threshold.
OBSERVATION: Metric, owner, window, and rollback threshold.
```

Before implementation, confirm the problem, smallest useful change, existing alternatives, and cost of doing nothing. Prototype code does not become production code without a reviewed Build Contract and the full shipping loop.

Record the Build Contract owner and revision. Create a separate decision record only for a choice that must outlive the change. Promote enduring decisions to project knowledge; delete stale text.

## 2. Add the technical plan and proof table

In the same working document, record:

- Existing patterns and interfaces to reuse.
- Proposed boundaries and affected data, APIs, permissions, and operations.
- Deployment or migration ordering.
- Small vertical slices and their dependencies.
- Consequential alternatives rejected and why.

Give important requirements stable IDs and map them to independent proof:

| ID | Requirement | Verification | Evidence owner |
| --- | --- | --- | --- |
| AC-1 | Duplicate payment requests do not double-charge | Integration test plus idempotency inspection | Reviewer |
| AC-2 | Existing refunds remain unchanged | Preservation suite | Independent reviewer |

Do not allow the implementation agent to weaken, delete, or redefine protected proof without approval. Passing visible tests is insufficient when the agent controls both the solution and all evaluation evidence.

For a consequential choice, record the owner, evidence, options rejected, rationale, assumptions, and what would reopen it. A weighted matrix can expose tradeoffs, but does not replace judgment.

## 3. Run the gap scan

Inspect only the dimensions relevant to the change:

| Dimension | Ask |
| --- | --- |
| Domain and data | Which entities, relationships, invariants, retention, or privacy rules change? |
| Actors and authority | Who may read, decide, write, approve, retry, or override? |
| System boundaries | Where do data or actions cross services, devices, vendors, or security boundaries? |
| Events and time | What triggers the behavior, in what order, with which states, retries, and terminal conditions? |
| Rules and recovery | What must always or never happen, and how is failure detected, repaired, or rolled back? |
| Interfaces | Which contracts, consumers, providers, versions, and owners are affected? |

Undefined intersections are questions—not permission for an agent to invent behavior. Give the Build Contract to a fresh reviewer and close any guess that could change the outcome.

For multiple services, state transitions, or non-trivial data flow, use a text-based state, sequence, entity, or flow diagram to expose structural gaps. Skip diagrams when the structure is obvious.

## 4. Set gates before implementation

- Run and record the baseline.
- Review and protect acceptance evidence.
- Add preservation, adversarial, boundary, concurrency, and abuse cases as appropriate.
- Define measurable reliability, security, privacy, performance, accessibility, cost, observability, and recovery requirements.
- Keep humans present for intent, unresolved consequential decisions, the first implementation checkpoint, irreversible actions, and final product or rollout judgment.

Before a high-impact action, stop and show the intended change, affected users and connections, required permission, proof, rollout, and rollback.

When a database migration applies, plan `expand -> migrate/backfill -> contract` before implementation so old and new versions can coexist:

- Sequence schema and application deployments.
- Prefer backward-compatible additions for populated tables.
- Validate database-specific locking and rewrite behavior.
- Create indexes concurrently or online where appropriate.
- Backfill large datasets in observable batches outside the schema transaction.
- Give one owner authority over migration ordering.
- Define proof for forward migration, coexistence, and the realistic rollback or roll-forward path.

## 5. Implement, verify, review, and harden

Follow the Quick Ship implementation loop in coherent slices. Verify each slice with its mapped proof, then before release:

- Run the integrated proof set and applicable project gates.
- Give the Build Contract, change, and evidence to a fresh adversarial reviewer, then route each finding to its cause.
- Verify the applicable quality limits using the [Verification hardening and security checklists](../reference/verification.md#hardening).
- Use advanced checks from [Verification](../reference/verification.md) when specification gaming or non-deterministic behavior makes ordinary tests insufficient.

## 6. Release and observe

Before release:

- [ ] Every proof-table item has current evidence.
- [ ] Applicable build, regression, type, lint, static, dependency, and security gates pass.
- [ ] The critical path was exercised in the running system.
- [ ] Permission and data-flow changes were reviewed.
- [ ] Required human gates and final technical or domain review are complete.
- [ ] Residual risks and rejected alternatives are recorded.
- [ ] The rollback or roll-forward path was exercised where practical; otherwise a compensating recovery plan is recorded.
- [ ] Rollout stages and entry/exit criteria, rollback, observation metric, owner, window, and rollback threshold are explicit.

Preserve the Build Contract revision, commands and results, tests and evals, permissions, human decisions, release identifier, and residual risks. After release, observe for the defined window, act if the rollback threshold is crossed, and record the result and decision.

Software correctness and product success are different claims. Feed incidents, support cases, and unexpected behavior back into the Build Contract, eval set, or project constraints.
