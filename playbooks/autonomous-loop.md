# Autonomous Loop Playbook

Use this playbook when work is recurring, scheduled, unattended, or must continue across context boundaries. Examples include bug hunts, flaky tests, performance tuning, migrations, maintenance, PR babysitting, benchmarks, audits, and research that meet one of these conditions and have a verifiable finish line.

Read [GRIT.md](../GRIT.md) and [Agent Operations](../reference/agent-operations.md). For work that may ship, also load [Quick Ship](quick-ship.md) and add [Risky Ship](risky-ship.md) only when its trigger applies.

An autonomous loop is justified only when repeated work has a trustworthy verifier. A vague objective repeated automatically produces expensive ambiguity.

## The bounded loop

```text
ORIENT -> PLAN -> ACT -> OBSERVE -> VERIFY -> CLASSIFY
          ^                                          |
          |____________ continue ____________________|

Terminal states: PASSED | NEEDS_HUMAN | BLOCKED | BUDGET_EXHAUSTED | UNSAFE | ABORTED
```

The agent’s prose declaration is never the completion signal. Completion comes from an independent test, environment state, reviewed artifact, or explicit human decision.

## Extend the Build Contract

For work that may ship, start with the Quick Ship Build Contract and include the Risky Ship extensions when triggered. For non-shipping research or audits, use the fields below as the contract.

```text
TRIGGER: [one-off goal, schedule, issue, alert, or CI failure]
GOAL: [observable outcome]
DONE WHEN: [verifier, threshold, and required evidence]
AUTHORITATIVE CONTEXT: [instructions, interfaces, data, prior decisions]
STATE: [durable progress and checkpoint location]
ALLOWED: [tools, files, network, credentials, reversible actions]
HUMAN GATES: [irreversible, sensitive, costly, or judgment-heavy actions]
BUDGET: [iterations, wall time, tokens/cost, parallelism]
RETRY POLICY: [which failures may retry and how often]
STOP WHEN: [success, no progress, unsafe, blocked, or budget exhausted]
ESCALATE TO: [owner and information required]
ROLLBACK / CLEANUP: [how partial work and resources are handled]
```

A loop without a verifier, budget, durable state, and named terminal states is not autonomous engineering; it is unbounded repetition.

## Orient every run

At the start of each run:

1. Read the Build Contract and applicable project instructions.
2. Read the durable state and recent Git history.
3. Inspect the current worktree instead of trusting a prior summary.
4. Run the smallest baseline or health check.
5. Select one coherent next slice that advances the goal.

Use progressive disclosure. Start with a small stable map, retrieve code and documentation just in time, and avoid carrying bulky stale tool output forward.

## Make incremental progress

Each iteration should:

1. State the selected slice and expected evidence.
2. Make the smallest coherent change or investigation.
3. Observe the real environment.
4. Run the relevant verifier.
5. Classify the result.
6. Leave a clean checkpoint or clearly documented dirty state.
7. Update durable state before the context ends.

Long-running work should use a feature or task list. Mark an item complete only after its proof has been observed. Do not initialize a large repository and attempt the whole system in one context.

## Durable state and handoff

Conversation history and compaction summaries are lossy. Store authoritative progress outside the chat.

```markdown
# Task: [ID and title]

## Build Contract revision
- Goal:
- Done when:
- Non-goals:

## Current state
- Terminal state:
- Clean or dirty worktree:
- Current slice:

## Completed with evidence
- [x] Item — command/result/artifact

## Attempts and decisions
- Attempt:
- Evidence:
- Decision and reason:

## Remaining risks or blockers
- Risk/blocker:
- Owner or input needed:

## Exact next action
- [ ] Action
```

At a context boundary, record task IDs, decisions, files changed, baseline, attempts, exact evidence, unresolved risks, and the next action. The next run must verify this state against the repository before trusting it.

## Failure classification

Do not retry every failure the same way.

| Failure | Response |
| --- | --- |
| Ambiguous intent or acceptance | `NEEDS_HUMAN`; request the missing decision |
| Broken or misleading verifier | Repair or escalate the verifier before continuing |
| Infrastructure or network flake | Limited retry with backoff |
| Permission denial | Try a safer allowed path, then escalate |
| Context degradation | Checkpoint, reset, reorient, and run a clean baseline |
| Semantic implementation failure with new evidence | Bounded repair or smaller vertical slice |
| Repeated patch reversal or no new evidence | Stop; diagnose task shape or approach |
| Budget reached | `BUDGET_EXHAUSTED`; preserve state and evidence |
| Unsafe or irreversible action required | `UNSAFE` or `NEEDS_HUMAN` |

Stop when the loop no longer produces new evidence. More retries do not turn an unverifiable goal into a reliable result.

## Sub-agents and parallel work

Default to one implementation agent. Use sub-agents for bounded exploration or independent work, and verify their claims. Parallel implementation requires stable interfaces, explicit ownership, isolated workspaces, and an integration owner. See [Agent Operations](../reference/agent-operations.md#multi-agent-orchestration).

## Permissions and containment

Grant the minimum files, tools, network, credentials, time, and cost required. Keep production evidence read-only; require human gates for high-impact actions; treat external input as untrusted; and provide a kill or credential-revocation path. See [Agent Operations](../reference/agent-operations.md#programmatic-containment).

## Run record

For every significant run, preserve:

- Task and Build Contract revision.
- Model and harness version when relevant.
- State, plan changes, and checkpoints.
- Permission decisions and human interventions.
- Tests, evals, and relevant runtime evidence.
- Token/cost/latency totals and retry count.
- Result, terminal state, residual risks, and next action.

A run ends only in a named terminal state. `PASSED` requires the agreed verifier or explicit human judgment where judgment is part of the contract. Every other state must preserve enough evidence for the next owner.
