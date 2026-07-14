# GRIT

**Guidelines and Rules for Iterating on Things (with AI)**

Lean contracts. Bounded loops. Fresh contexts. Independent evidence. Verification before trust.

---

## How to use this repository

This file is the canonical framework: read it to understand GRIT and choose the right operating mode. Load only the playbook and reference material required for the current task.

| Need | Load |
| --- | --- |
| First time using GRIT, especially as a PM or founder | [Founder/PM Quickstart](playbooks/founder-pm-quickstart.md) |
| Any normal shipping task—start here | [Quick Ship](playbooks/quick-ship.md) |
| A visible risk trigger applies | [Risky Ship](playbooks/risky-ship.md) |
| Long-running, scheduled, recurring, or unattended agent work | [Autonomous Loop](playbooks/autonomous-loop.md) |
| Tests, evals, hardening, security gates, and release evidence | [Verification](reference/verification.md) |
| Context, permissions, architecture, Git, multi-agent work, and dependencies | [Agent Operations](reference/agent-operations.md) |

For AI agents: do not load every file by default. Start with applicable project instructions, the task contract, and one playbook. Retrieve a reference only when the task needs it.

### Authority and precedence

GRIT does not override organization policy, repository instructions, or human authorization. A useful order is:

```text
Organization policy
  -> repository and directory instructions
    -> task or delivery contract
      -> selected GRIT playbook and reference
        -> current plan and progress state
```

More specific guidance refines work inside higher-level safety and authorization boundaries. Surface conflicts rather than silently choosing one source.

---

## What GRIT is

GRIT is a lightweight, risk-adaptive shipping system for product managers, founders, indie hackers, startup teams, and engineers using AI agents to build meaningful software. It works with any environment that can inspect files, edit code, run tools, and review changes.

GRIT is environment-, model-, and vendor-agnostic. It specifies capabilities, evidence, and control boundaries—not a particular IDE, command syntax, agent product, model family, or provider. Adapt its examples to the project’s native tools.

AI can produce plausible code that quietly fails or confidently implements the wrong intent. The answer is not only a smarter model or a longer prompt. It is a disciplined system that:

- Keeps intent and success criteria human-owned.
- Gives agents relevant, navigable context.
- Constrains tools and permissions technically.
- Works in small, observable increments.
- Separates implementation from final judgment.
- Requires reproducible evidence before completion.
- Converts production and review failures into durable improvements.

GRIT is not an airtight specification process. Implementation and prototypes create learning. The first contract is a hypothesis good enough to begin; when learning changes the contract, update it before asking the code to follow it.

GRIT applies to two related scopes:

- **Build agents** work in the development environment to create or change software. GRIT controls their context, permissions, execution, review, and evidence.
- **Product agents** operate inside the shipped product or a business process. They need the same delivery discipline plus runtime evals, tool and data boundaries, traceability, safety behavior, monitoring, and recovery.

Do not confuse the agent that builds a product with an agent the product delivers. The second is also production behavior and must be specified, tested, operated, and observed as such.

## Who it is for

- Product managers who can define the problem and desired behavior but may not own the technical implementation.
- Indie hackers and solo founders shipping real products with AI agents.
- Fast-moving startups that need proportional rigor rather than enterprise ceremony.
- Engineers who want speed without faith-based review.
- Product and engineering leads who need a shared definition of evidence.
- Systems involving business rules, user data, payments, auth, agents, integrations, or operational risk.
- Skeptical developers willing to use AI with enforceable boundaries.

It is most valuable when an agent is doing more than autocomplete: creating features, changing data models, integrating systems, refactoring, writing tests, operating tools, or working across multiple contexts.

GRIT does not ask a PM or founder to invent architecture, write tests, or understand every line of code. It separates ownership clearly:

| PM or founder owns | Agent or engineer owns |
| --- | --- |
| Problem, target user, and desired outcome | Repository and system investigation |
| Smallest useful scope and non-goals | Technical options and recommended approach |
| User-visible behavior and business constraints | Implementation plan, tasks, and code |
| Acceptable risk and consequential product decisions | Risk discovery and mitigation options |
| What convincing product proof looks like | Tests, technical checks, and evidence collection |
| Final product judgment and release decision | Clear reporting of results, limits, and residual risks |

The agent may challenge, clarify, and recommend. It must not silently redefine product intent. The PM does not need to dictate implementation, but remains accountable for what is built and released.

---

## The two loops

### The delivery loop

Every non-trivial production change follows the same outer lifecycle:

```text
SPEC -> TEST -> IMPLEMENT -> REVIEW -> HARDEN -> SHIP
  ^                                                |
  |_____________ route findings back ______________|
```

1. **Spec** — define intent, expected behavior, boundaries, risk, and proof well enough to begin.
2. **Test** — establish the baseline and create evidence that would fail without the change.
3. **Implement** — make the smallest coherent change that satisfies the contract.
4. **Review** — use a fresh context to attack the result against intent and evidence.
5. **Route findings** — repair the contract, proof, implementation, or project system that caused the problem.
6. **Harden** — verify non-functional requirements and search for omitted risk.
7. **Ship** — release only with reproducible evidence, rollback, and appropriate observation.

The lifecycle is stable; its ceremony changes with risk.

### The execution loop

Inside implementation, a long-running agent uses a smaller bounded control loop:

```text
ORIENT -> PLAN -> ACT -> OBSERVE -> VERIFY -> CLASSIFY
                    ^                    |
                    |______ retry _______|

Terminal states: PASSED | NEEDS_HUMAN | BLOCKED | BUDGET_EXHAUSTED | UNSAFE | ABORTED
```

The agent saying “done” is not a terminal condition. Completion comes from an independent test, observable environment state, reviewed artifact, or explicit human decision.

Use the [Autonomous Loop playbook](playbooks/autonomous-loop.md) whenever work spans multiple iterations, contexts, schedules, or unattended periods.

---

## Quick Ship is the default

Begin every shipping task with [Quick Ship](playbooks/quick-ship.md). Move to [Risky Ship](playbooks/risky-ship.md) only when at least one visible trigger applies:

- Authentication or permissions.
- Payments.
- Sensitive user data.
- Database migrations.
- Destructive actions.
- Public APIs or contracts other systems depend on.
- Changes that are difficult to undo.

If none applies, stay in Quick Ship. If investigation discovers a trigger, pause and switch before implementation. Uncertainty by itself calls for investigation or a Spike; it does not require the full Risky Ship process unless it reveals one of these risks.

### Startup speed principle

Use the minimum process that prevents avoidable rework. Every artifact, meeting, agent pass, and check must clarify intent, reduce a visible risk, or produce reproducible evidence. If it does none of those things, skip it. Agent speed matters only when it reduces time to an accepted outcome—not when it merely generates more code faster.

### Other modes

| Mode | Use when | Minimum bar |
| --- | --- | --- |
| **Spike** | Exploring an idea that may be discarded | Optimize for learning; spike code does not ship until rewritten or put through a shipping playbook |
| **Quick Ship** | Default for normal shipping work | Compact contract, baseline, test or manual proof, fresh review, rollback |
| **Risky Ship** | One or more visible risk triggers above applies | Reviewed artifact stack, protected evidence, human gates, hardening, rollout, rollback, observation |
| **Autonomous Loop** | Repeated or long-running work with a machine-checkable finish line | Goal, verifier, durable state, permissions, budget, retry policy, terminal states, escalation |

Modes can compose. A risky migration performed over multiple sessions uses both Risky Ship and Autonomous Loop.

Faster generation may shorten execution cycles, but it does not remove discovery, review, integration, or rollout constraints. Gate work by risk and evidence rather than a fixed ceremony or the raw speed of an agent.

### Scale by risk, complexity, and uncertainty

| Situation | Minimum rigor |
| --- | --- |
| Copy, comments, tiny config | One-shot check |
| UI styling or layout | Compact contract and visual proof |
| Core logic without a Risky Ship trigger | Quick Ship with automated proof |
| Database migration | Risky Ship plus migration verification |
| Public API or contract | Risky Ship plus fixture, mock, sandbox, or real integration proof |
| Authentication, permissions, payments, or sensitive user data | Risky Ship with independent and human review |
| Recurring maintenance or long investigation | Applicable shipping mode plus Autonomous Loop |

---

## Step 0: Challenge the premise

Before specifying a feature, answer:

```text
PROBLEM: [What problem does this solve, for whom, and how is it handled today?]
SMALLEST: [What is the smallest version that delivers or tests value?]
EXISTS: [Does this capability already exist under another name?]
NULL CASE: [What happens if nothing is built?]
```

For strategically uncertain work, also record user evidence, the riskiest assumption, the desired outcome metric, and the condition for continuing or killing the idea.

If the problem cannot be stated clearly, discovery is not finished. If the capability already exists, reuse or stop. If doing nothing has no meaningful cost, reconsider its priority.

The answers stay with the work so a future human or agent can understand why it exists.

---

## The contract is the leverage

Separate three concerns:

- **Intent** — the outcome and why it matters.
- **Expectations** — success, failure, preservation requirements, non-goals, and what counts as evidence.
- **Context** — existing facts, interfaces, decisions, and constraints the agent must inspect rather than invent.

The human owns intent and expectations. Agents may help discover context and expose ambiguity, but should not silently decide what “done” means.

A prompt directs a turn. Context supplies the facts, tools, and state needed for that turn. Intent, constraints, and verification govern action across turns. Better prompting does not replace the other layers.

Prefer a delta contract for existing systems: current behavior, new behavior, and what must remain unchanged. Use the smallest artifact set that remains clear:

- Quick Ship normally uses one compact contract.
- Risky Ship may separate discovery brief, delivery contract, technical plan/task graph, and proof map.
- Autonomous work adds a loop contract containing verifier, state, permissions, budget, retry policy, and terminal states.

Specs are living hypotheses, not waterfall gates. When implementation reveals a missing case or changed requirement, update the contract and proof before the implementation. A contract bug patched only in code will return in a later session.

The playbooks contain copy-ready templates.

### Give artifacts a lifecycle

Important contracts and decision records should identify their status, owner, date or revision, and what they supersede. Write them so a future reader can reconstruct not only what was chosen, but why, under which assumptions, and what evidence would reopen the decision.

After the change:

- Promote durable product or domain truths to maintained repository knowledge.
- Promote architectural choices to the project’s decision log.
- Turn enforceable behavior into tests, schemas, policies, or CI.
- Keep change-specific notes with the change record.
- Delete or clearly mark stale and superseded text.

Markdown is a coordination artifact, not automatically an eternal source of truth.

---

## Evidence before implementation

Before editing an existing system:

1. Read applicable instructions and the task contract.
2. Search for existing implementations, patterns, interfaces, and tests.
3. Run the smallest relevant baseline check.
4. Record pre-existing failures.
5. Create or identify the proof that would distinguish success from a plausible-looking change.

At minimum, meaningful deterministic behavior needs a happy path, an edge case, and an error or boundary case. Bug fixes begin by reproducing the bug.

Low-risk visual, copy, or configuration work may use an exact manual proof. Risky work needs protected acceptance evidence, preservation tests, and fresh adversarial cases. Non-deterministic LLM or agent behavior needs semantic evals and repeated trials, not only schema assertions.

See [Verification](reference/verification.md) for the complete ladder.

---

## Implement in coherent slices

- Search before creating code or adding dependencies.
- Follow established interfaces and architecture.
- Make one coherent vertical slice at a time.
- Keep scope inside the contract.
- Record consequential assumptions and structural decisions.
- Escalate decisions affecting intent, architecture, public contracts, data, security, permissions, cost, or irreversible state.

One logical slice may touch many files. The limit is whether it can be understood and verified independently, not an arbitrary file count.

Use fresh context when the prior conversation is exploratory, stale, or filled with failed attempts. For long work, store authoritative progress outside the conversation and leave reproducible checkpoints.

Do not retry blindly. Classify failure: ambiguity belongs to the contract owner, a broken grader belongs to verification, an infrastructure flake may get a limited retry, context degradation needs a checkpoint and reset, and repeated no-progress failure needs decomposition or escalation.

---

## Review without loyalty to the implementation

Use a fresh context and the strongest appropriate reviewer. Model diversity can help, but independence, relevant expertise, and access to evidence matter more.

Review should attack:

- Logic bugs and silent failures.
- Security, privacy, permission, and operational risks.
- Contract violations and intent drift.
- Missing preservation, edge, error, concurrency, or abuse cases.
- Weak or tautological tests and graders.
- Unnecessary scope and premature abstraction.
- Claims that exceed the available evidence.

Humans remain responsible for product, domain, ethical, and irreversible judgments. A late approval on an incomprehensible diff is not ownership.

---

## Route findings to their cause

Do not patch every symptom in the implementation.

| Finding | Route to |
| --- | --- |
| Intent or behavior is ambiguous | Contract, then proof and implementation |
| Proof misses a real scenario | Test/eval, then implementation |
| Implementation violates a correct contract | Implementation |
| Environment prevents reliable work | Tooling, bootstrap, context, or observability |
| Agent exceeded authority | Permission model or control plane |
| Product result is poor despite correct software | Discovery, outcome metric, or product decision |

Small findings should cause small upstream corrections. The purpose is to prevent recurrence, not restart the entire process ceremonially.

---

## Harden and ship with evidence

Functional tests do not prove reliability, security, accessibility, performance, privacy, cost, or production readiness. Risky Ship defines measurable non-functional requirements before implementation; hardening verifies them and searches for omissions.

Before declaring completion:

- Applicable tests, type checks, lint, static, dependency, and security gates pass.
- User-facing behavior is exercised in the running system.
- Protected evidence has not been weakened without approval.
- Residual risks and consequential decisions are recorded.
- Rollout and rollback are understood.
- User, data, billing, auth, permission, or availability changes have an observation owner, window, metric, and rollback threshold.

Preserve commands, results, screenshots, logs, metrics, traces, human interventions, and the final state at the depth the risk justifies.

Correct implementation and product success are different claims. Observe actual outcomes and be willing to roll back or kill a feature that was built correctly but does not create value.

---

## Keep the system learning

A static methodology rots. Recurring failures should improve the layer that caused them:

- Missing expected behavior becomes a regression test or eval.
- An enforceable invariant becomes a linter, structural test, schema, or policy.
- A missing capability becomes a better tool, bootstrap, or observable environment.
- A context failure becomes improved retrieval, scoped documentation, or durable state.
- A permission failure becomes a technical boundary or approval gate.
- A one-off implementation error is fixed without adding a permanent rule.
- A harness change states its expected benefit and is kept only if results improve.

Prefer executable enforcement over another paragraph of instructions. Keep rules short, scoped, owned, searchable, and regularly pruned. Remove scaffolding that stronger models or better tools make unnecessary.

Track which contract formats and levels of detail produce clean first-pass implementations. Over time, learn the project’s “spec minimum”: the point below which the agent starts inventing consequential behavior. It will differ by codebase and change type.

### Watch for intent drift

Intent drift is the gradual divergence between what the product should do and what the code does. Agents accelerate it by filling unspecified gaps with plausible generic defaults.

Signals include:

- Review repeatedly finds behavior the contract never mentioned.
- Users say “it works, but it is not what I expected.”
- Tests pass while a manual walkthrough reveals wrong defaults or missing constraints.
- New sessions relitigate decisions because their rationale was never stored.

Fix intent drift upstream: improve boundaries, preservation requirements, decision records, examples, and proof.

### Measure human leverage, not AI output

Track at least informally:

- End-to-end lead time and first-pass acceptance.
- Human steering and review time.
- Rework by cause.
- Cost per accepted change.
- Escaped defects and rollbacks.
- Context resets and permission interventions.
- Reliability across repeated trials for stochastic systems.

Do not optimize lines generated, agent count, or PR count without accepted outcomes. A short periodic review of what kept breaking—and which system change prevented it—is enough to keep GRIT alive.
