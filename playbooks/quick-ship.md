# Quick Ship Playbook

This is GRIT’s default shipping playbook. Start here for every product or engineering change that may ship.

Add [Risky Ship](risky-ship.md) when the change touches authentication, permissions, security boundaries, or agents taking consequential actions; payments; sensitive user data; database migrations; destructive actions; public APIs or contracts other systems depend on; or changes that are difficult to undo. Add the [Autonomous Loop](autonomous-loop.md) when work is recurring, scheduled, unattended, or must continue across context boundaries.

## Minimum bar

- A Build Contract with intent, boundaries, proof, and rollback.
- A known baseline or recorded pre-existing failures.
- Search before creating code or dependencies.
- An automated check or exact manual proof.
- One coherent vertical slice at a time.
- A fresh review against the Build Contract.
- Short post-release observation for user-facing changes.

## 1. Agree the Build Contract

Use only the fields the change needs, but do not omit intent, boundaries, proof, or rollback.

Before writing it, ask whether the problem is real and worth solving, whether the capability already exists, and what happens if nothing is built.

```text
PROBLEM AND EVIDENCE:
SMALLEST USEFUL CHANGE:
CURRENT BEHAVIOR: [optional for new behavior]
EXPECTED BEHAVIOR:
EXPECTED OUTCOME: [optional for non-product work]
WHAT MUST STILL WORK:
NON-GOALS:
CONTEXT, RISKS, AND CONNECTIONS:
PROOF:
ROLLBACK:
OPEN DECISIONS:
```

For an existing system, describe the delta rather than restating the whole product. A reversible, low-risk assumption may be recorded and tested. Escalate decisions affecting product intent, architecture, public contracts, data, security, permissions, cost, or irreversible state.

The Build Contract is authoritative for the current pass. When implementation reveals a missing case or changed requirement, update the contract and proof before the code.

## 2. Investigate and establish proof

Before editing:

1. Read applicable project instructions.
2. Search for existing implementations, patterns, interfaces, and tests.
3. Run the smallest relevant baseline check.
4. Record pre-existing failures.
5. Identify what would distinguish success from a plausible-looking change.
6. Recommend the smallest technical approach and surface consequential questions.

Meaningful deterministic behavior normally needs a happy path, an edge case, and an error or boundary case. Reproduce a bug before accepting its fix. Low-risk visual, copy, or configuration work may use an exact manual walkthrough, screenshot, command, response, or log instead of an automated test.

An agent may draft the checks, but review them for tautology and implementation coupling.

## 3. Implement one vertical slice

```text
Implement the smallest coherent change that satisfies the Build Contract.
Search before creating code or adding dependencies.
Follow established interfaces and patterns.
Do not add behavior outside the contract.
Stop for consequential decisions.
```

One slice may touch many files. The limit is whether it can be understood and verified independently, not an arbitrary file count.

Record consequential assumptions and structural decisions where the next session can find them. After two failed correction cycles without meaningful new evidence, stop patching and diagnose the contract, proof, context, task size, or technical approach.

## 4. Verify the result

After implementation:

1. Run the agreed proof and relevant project checks from the known baseline.
2. Exercise user-facing behavior in the running system where practical.
3. Confirm behavior that must remain unchanged still works.
4. Preserve the command, result, screenshot, response, or log needed to reproduce the conclusion.

If the proof is weak or the contract changed, fix that upstream before continuing.

## 5. Run a fresh review

Use a fresh context and provide the Build Contract, plan, diff, and evidence.

```text
Review this change against the Build Contract. Be adversarial.

Find:
- Logic bugs and silent failures
- Security, privacy, or permission issues
- Contract violations and intent drift
- Missing preservation, edge, or failure cases
- Weak or tautological proof
- Unnecessary scope

Return findings by severity and route each one to the
contract, proof, implementation, or project constraint that must change.
```

The reviewer should understand the intent before reading the diff. Independence and access to evidence matter more than brand or model diversity.

## 6. Route, harden, and ship

| Finding | Route to |
| --- | --- |
| Intent or behavior is ambiguous | Build Contract, then proof and implementation |
| Proof misses a real scenario | Proof definition or check, then implementation |
| Implementation violates a correct contract | Implementation |
| Environment prevents reliable work | Tooling, context, or observability |

Before completion:

- [ ] Relevant tests, type checks, and lint pass from a known baseline.
- [ ] User-facing behavior was exercised in the running system.
- [ ] The diff contains only the intended logical change.
- [ ] Fresh review has no unresolved blockers.
- [ ] Evidence is reproducible.
- [ ] Rollback is known.
- [ ] A user-facing change has an observation owner and window.

Harden only against applicable risks discovered during investigation, verification, or review. If a Risky Ship trigger appears later, pause and add its requirements before continuing.

The agent saying “done” is not evidence. Completion is an observed state that satisfies the Build Contract.
