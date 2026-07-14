# GRIT

**Guidelines and Rules for Iterating on Things (with AI)**

Lean contracts. Bounded loops. Fresh contexts. Independent evidence. Verification before trust.

---

## How to use GRIT

Start with [README.md](README.md) if you are new to GRIT. For a task, load only the Build Contract, applicable project instructions, and the playbook the work requires:

| Need | Load |
| --- | --- |
| Any normal shipping task | [Quick Ship](playbooks/quick-ship.md) |
| A visible risk trigger applies | [Quick Ship](playbooks/quick-ship.md) plus [Risky Ship](playbooks/risky-ship.md) |
| Work is recurring, scheduled, unattended, or must continue across context boundaries | [Autonomous Loop](playbooks/autonomous-loop.md); also load the shipping playbook if the output may ship |
| Tests, evals, hardening, security, or release evidence | [Verification](reference/verification.md) |
| Context, permissions, architecture, Git, dependencies, or parallel agents | [Agent Operations](reference/agent-operations.md) |

Do not load every file by default. Progressive disclosure protects both human attention and agent context.

GRIT does not override organization policy, repository instructions, or human authorization. Surface conflicts instead of silently choosing the convenient instruction; [Agent Operations](reference/agent-operations.md) defines precedence.

---

## What GRIT protects

GRIT is a lightweight, risk-adaptive shipping system for product managers, founders, indie hackers, startup teams, and engineers using AI agents to build meaningful software. It is environment-, model-, and vendor-agnostic.

AI can produce plausible code that quietly fails or confidently implements the wrong intent. GRIT addresses that failure mode by:

- Keeping intent and success criteria human-owned.
- Giving agents relevant, navigable context.
- Constraining tools and permissions technically.
- Working in small, observable increments.
- Separating implementation from final judgment.
- Requiring reproducible evidence before completion.
- Turning review and production failures into durable improvements.

GRIT is not an airtight specification process. The first Build Contract is a hypothesis good enough to begin. When prototypes, implementation, or user learning change what “right” means, update the contract and proof before asking the code to follow it.

GRIT covers agents that build software and agents embedded inside a product or business process. Product agents additionally require runtime evals, tool and data boundaries, traceability, safety behavior, monitoring, and recovery.

Humans own the problem, intended behavior, acceptable risk, convincing proof, and release decision. Agents and engineers investigate the system, recommend an approach, implement it, and report evidence and residual risk. The agent may challenge intent but must not silently redefine it. [README.md](README.md#who-owns-what) contains the practical ownership split.

---

## Quick Ship is the default

Begin every shipping task with [Quick Ship](playbooks/quick-ship.md). Add [Risky Ship](playbooks/risky-ship.md) only when the change touches:

- Authentication, permissions, security boundaries, or agents taking consequential actions.
- Payments.
- Sensitive user data.
- Database migrations.
- Destructive actions.
- Public APIs or contracts other systems depend on.
- Changes that are difficult to undo.

If none applies, stay in Quick Ship. If investigation discovers a trigger, pause and add Risky Ship before implementation. Risky Ship adds requirements to the same delivery loop; it is not a second process. Uncertainty alone calls for investigation or a disposable Spike; it does not require Risky Ship unless it reveals a trigger.

Use the minimum process that prevents avoidable rework. Every artifact, meeting, agent pass, and check must clarify intent, reduce a visible risk, or produce reproducible evidence. If it does none of those things, skip it.

A **Spike** optimizes for learning and does not ship without a Build Contract and shipping playbook. Add **Autonomous Loop** when work is recurring, scheduled, unattended, or must continue across context boundaries. Modes compose: a long-running migration uses Quick Ship, Risky Ship, and Autonomous Loop together.

---

## The two loops

Every non-trivial production change follows the delivery loop:

```text
CONTRACT -> INVESTIGATE -> IMPLEMENT -> VERIFY -> REVIEW -> HARDEN -> SHIP
    ^                                                                    |
    |____________________ route findings back ___________________________|
```

1. **Contract** — agree the Build Contract: intent, behavior, boundaries, risk, proof, and rollback.
2. **Investigate** — inspect the existing system, establish the baseline, and validate the proof and technical plan before editing.
3. **Implement** — make the smallest coherent change that satisfies the contract.
4. **Verify** — run the agreed proof and applicable project checks against the changed system.
5. **Review** — use a fresh context to attack the result against intent and evidence.
6. **Route findings** — repair the contract, proof, implementation, or project system that caused the problem.
7. **Harden** — verify applicable non-functional requirements and search for omitted risk.
8. **Ship** — release only with reproducible evidence, rollback, and appropriate observation.

Inside long-running work, use a bounded execution loop:

```text
ORIENT -> PLAN -> ACT -> OBSERVE -> VERIFY -> CLASSIFY
          ^                                          |
          |____________ continue ____________________|

Terminal states: PASSED | NEEDS_HUMAN | BLOCKED | BUDGET_EXHAUSTED | UNSAFE | ABORTED
```

The agent saying “done” is not a terminal condition. Completion comes from an independent check, observable environment state, reviewed artifact, or explicit human decision.

---

## The Build Contract

A Build Contract is the smallest durable agreement that keeps implementation aligned. It separates:

- **Intent** — the outcome and why it matters.
- **Expectations** — expected behavior, preservation requirements, non-goals, risk, and proof.
- **Context** — existing facts, interfaces, decisions, and constraints the agent must inspect rather than invent.

Before building, challenge the premise:

```text
PROBLEM AND EVIDENCE: What problem does this solve, for whom, and why do we believe it matters?
SMALLEST: What is the smallest version that delivers or tests value?
EXISTS: Does this capability already exist under another name?
NULL CASE: What happens if nothing is built?
```

If the problem is unclear, discovery is not finished. If the capability exists, reuse or stop. For uncertain product bets, also record the riskiest assumption, desired outcome, and condition for continuing or killing the idea.

Prefer a delta contract for existing systems: what changes, what stays the same, and how both are proved. The copy-ready shipping template lives in [Quick Ship](playbooks/quick-ship.md#1-agree-the-build-contract). Risky Ship extends it when a risk trigger applies; Autonomous Loop adds loop controls and can stand alone for non-shipping research or audits.

Important contracts and decisions should identify their owner, revision, assumptions, and what would reopen them. After the change, promote durable truths to maintained project knowledge, convert enforceable behavior into tests or policy, and delete or mark stale text.

---

## Investigate before action

Before editing an existing system:

1. Read applicable instructions and the Build Contract.
2. Search for existing implementations, patterns, interfaces, tests, and dependencies.
3. Run the smallest relevant baseline check and record pre-existing failures.
4. Create or identify proof that would fail without the intended change.

Meaningful deterministic behavior normally needs a happy path, an edge case, and an error or boundary case. Bug fixes begin by reproducing the bug. Low-risk visual or configuration work may use exact manual proof.

Risky work needs protected acceptance evidence, preservation tests, and fresh adversarial cases. Non-deterministic AI behavior needs versioned examples and repeated runs that test meaning, not only output shape. [Verification](reference/verification.md) defines the full ladder.

---

## Implement in coherent slices

- Search before creating code or dependencies.
- Follow established interfaces and architecture.
- Make one coherent vertical slice at a time.
- Keep scope inside the Build Contract.
- Record consequential assumptions and structural decisions.
- Escalate decisions affecting intent, architecture, public contracts, data, security, permissions, cost, or irreversible state.

One slice may touch many files. The limit is whether it can be understood and verified independently.

Use fresh context when the prior conversation is stale or filled with failed attempts. Store authoritative progress outside the conversation for long work. Do not retry blindly: classify ambiguity, broken verification, infrastructure failure, permission denial, context degradation, or no progress and route each to its owner.

---

## Verify the result

After each coherent slice, run the agreed proof and applicable project checks from the known baseline. Exercise user-facing behavior in the running system where practical, and preserve the command, result, or artifact another person or agent needs to reproduce the conclusion.

Proof must establish the Build Contract’s claims, not merely confirm that the code compiles or that the agent completed its steps. If implementation reveals that the proof is weak or the contract is wrong, route the finding upstream before continuing.

---

## Review and route without loyalty

Use a fresh context and the strongest appropriate reviewer. Independence, relevant expertise, and access to evidence matter more than product or model diversity.

Review should attack logic, security, privacy, permissions, intent drift, preservation, edge and error cases, concurrency, abuse, weak proof, unnecessary scope, premature abstraction, and claims that exceed the evidence.

Route findings to their cause:

| Finding | Route to |
| --- | --- |
| Intent or behavior is ambiguous | Build Contract, then proof and implementation |
| Proof misses a real scenario | Proof definition or check, then implementation |
| Implementation violates a correct contract | Implementation |
| Environment prevents reliable work | Tooling, context, or observability |
| Agent exceeded authority | Permission model or technical boundary |
| Product result is poor despite correct software | Discovery or product decision |

Small findings cause small upstream corrections. A contract bug patched only in code will return.

---

## Harden, ship, and observe

Functional tests do not prove reliability, security, accessibility, performance, privacy, cost, or production readiness. Risky Ship defines measurable non-functional requirements before implementation; hardening verifies them.

Before completion:

- Applicable tests, type checks, lint, static, dependency, and security gates pass.
- User-facing behavior is exercised in the running system.
- Protected proof has not been weakened without approval.
- Residual risks and consequential decisions are recorded.
- Rollout and rollback are understood.
- User-facing changes have an observation owner and window; Risky Ship also requires a metric and rollback threshold.

Preserve reproducible evidence at the depth the risk justifies. Correct software and product success are different claims; observe actual outcomes and be willing to roll back or kill a feature that does not create value.

---

## Keep the system learning

Recurring failures should improve the layer that caused them:

- Missing behavior becomes a regression test or eval.
- An enforceable invariant becomes a linter, schema, structural test, or policy.
- A missing capability becomes better tooling, bootstrap, or observability.
- A context failure becomes improved retrieval, scoped documentation, or stored progress.
- A permission failure becomes a technical boundary or approval gate.
- A one-off implementation error is fixed without adding a permanent rule.

Prefer executable enforcement over another paragraph. Keep rules short, scoped, owned, searchable, and regularly pruned. Learn the project’s “contract minimum”: the point below which agents start inventing consequential behavior.

Watch for intent drift: behavior the contract never mentioned, tests that pass while walkthroughs fail, users saying “it works, but not as expected,” or later sessions relitigating undocumented decisions. Fix drift upstream in the Build Contract, decision record, and proof.

Measure human leverage rather than AI output: lead time, first-pass acceptance, steering and review time, rework by cause, cost per accepted change, escaped defects, rollbacks, context resets, interventions, and repeated-run reliability. Do not optimize lines generated, agent count, or pull-request count without accepted outcomes.
