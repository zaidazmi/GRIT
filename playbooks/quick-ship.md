# Quick Ship Playbook

This is GRIT’s default shipping playbook. Start here for every normal product or engineering change. Read [GRIT.md](../GRIT.md) for the governing principles. If this playbook conflicts with project instructions or the task contract, surface the conflict before editing.

Switch to [Risky Ship](risky-ship.md) only if investigation reveals authentication or permissions, payments, sensitive user data, a database migration, a destructive action, a public API or dependent contract, or a change that is difficult to undo.

## Minimum bar

- A compact behavioral contract.
- A clean baseline or recorded pre-existing failures.
- Search before creating new code.
- An automated test or exact manual proof.
- A fresh review against the contract.
- A rollback path and, for user-facing changes, a short observation window.

If none of those visible triggers applies, remain in Quick Ship. Do not add Risky Ship ceremony merely because a change is technically interesting or the agent can generate a longer plan.

## 1. Write the contract

Use only the fields the change needs, but do not omit intent, boundaries, or proof.

```text
INTENT:
USER VALUE:
CURRENT BEHAVIOR:
NEW BEHAVIOR:
WHAT STAYS THE SAME:
SUCCESS SCENARIOS:
FAILURE OR EDGE SCENARIOS:
CONNECTIONS:
CONTEXT TO INSPECT:
DOES NOT DO:
TEST OR MANUAL PROOF:
ROLLBACK:
UNCLEAR OR ASSUMED:
```

For new behavior, `CURRENT BEHAVIOR` and `WHAT STAYS THE SAME` may be omitted. For an existing system, prefer this delta contract over restating the whole product.

Resolve blocking uncertainty before implementation. A reversible, low-risk assumption may be recorded and tested. Escalate choices that affect intent, architecture, public contracts, data, security, permissions, cost, or irreversible state.

### Example

```text
FEATURE: Order Total Calculator

DOES: Takes line items + optional discount code. Returns subtotal, discount, tax, total.
INPUTS: { items: LineItem[], discountCode?: string, taxRate: number }
OUTPUTS: { subtotal: number, discount: number, tax: number, total: number }

EDGE CASES:
- Empty items array returns all zeros.
- Invalid discount code is ignored.
- Negative quantity returns a validation error.

DOES NOT: Persist order. Validate inventory. Charge payment.
UNCLEAR: Should tax be calculated before or after discount?
```

## 2. Establish the baseline

Before editing:

1. Read the root and directory-scoped agent instructions.
2. Search for existing implementations, utilities, tests, interfaces, and naming patterns.
3. Run the smallest relevant test, build, type, or lint command.
4. Record any existing failure so it cannot be hidden inside the change.

Every fresh session starts blind. Give the agent the contract, relevant interfaces, and pointers to authoritative context. Let it retrieve implementation details progressively instead of pasting the entire repository into the prompt.

## 3. Create the proof

For behavior that matters, start with at least:

- One happy path.
- One edge case.
- One error or boundary case.

For a bug, reproduce it first with a failing test or exact observable procedure. If the bug was never reproduced, the fix is mostly theater.

An agent may draft tests, but review them for tautology and implementation coupling. For low-risk UI, copy, or configuration changes, an exact manual proof may replace an automated test: browser path, CLI command, API request, screenshot, log line, or another observable result.

## 4. Implement one vertical slice

```text
Implement the smallest change that satisfies this contract and its proof.
Search the repository before creating code.
Follow established interfaces and patterns.
Do not add behavior outside the contract.
If a consequential decision is unclear, stop and surface it.
```

One logical change may touch many files. The limit is coherence, not file count. Split work by independently useful vertical slices rather than arbitrary layers or file limits.

If implementation reveals that the contract is wrong or incomplete, update the contract and proof before changing the code again. Do not leave the next session with a stale description of reality.

Record structural decisions made during implementation: what was chosen, why, and which alternatives were rejected. One useful line is enough.

Two failed correction cycles without meaningful new evidence are a default diagnostic trigger: stop patching, classify the failure, and either repair the contract or proof, decompose the task, reset context, or escalate. A project may tune the number, but should keep a concrete no-progress threshold.

## 5. Run a fresh review

Use a fresh context and provide the contract, proof, and diff.

```text
Review this change against the contract. Be adversarial.

Find:
- Logic bugs and silent failures
- Security or privacy issues
- Contract violations and intent drift
- Missing preservation, edge, or failure cases
- Weak or tautological tests
- Unnecessary scope

Return findings by severity. For each finding, route it to the
contract, proof, implementation, or project constraint that must change.
```

Review the plan and intent before reading the diff line by line. The plan explains why; the diff proves what happened.

## 6. Route and finish

| Finding | Route to |
| --- | --- |
| Contract is ambiguous or missing behavior | Update the contract, then cascade |
| Proof misses a real scenario | Add the test or manual check, then re-implement |
| Implementation violates the contract | Fix the implementation |
| Implementation works but the contract was wrong | Update the contract and proof, then cascade |

Before completion:

- [ ] Relevant tests pass from a known baseline.
- [ ] Type checks and lint pass where applicable.
- [ ] User-facing behavior was exercised in the running system.
- [ ] The diff contains only the intended logical change.
- [ ] The fresh review has no unresolved blocking findings.
- [ ] Evidence is recorded: commands, results, screenshots or traces where relevant.
- [ ] The rollback path is known.
- [ ] A user-facing change has an observation owner, window, and rollback threshold.

The agent saying “done” is not evidence. Completion is an observed state that satisfies the contract.
