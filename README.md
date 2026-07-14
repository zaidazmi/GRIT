# GRIT

**Guidelines and Rules for Iterating on Things (with AI)**

Lean contracts. Bounded loops. Fresh contexts. Independent evidence. Verification before trust.

GRIT helps product managers, founders, indie hackers, startup teams, and engineers ship reliable software with AI coding agents. It is designed for fast-moving teams, not only established engineering organizations.

You do not need to dictate the architecture or understand every line of code. You do need to own the problem, boundaries, risk, and final product judgment—and require evidence before shipping.

GRIT is IDE-, agent-, model-, and provider-agnostic. It works anywhere an agent can inspect a project, edit code, run its native checks, and preserve evidence.

## Why GRIT exists

I’m a product person, not a traditional software engineer. After more than a decade building startups alongside developers—reviewing releases, debating architecture, and debugging production failures—AI agents gave me the ability to ship software directly. They also introduced a new class of failure: code that looks finished, passes a casual review, and is quietly wrong.

GRIT is the accumulated scar tissue from building with AI every day: the smallest set of habits that consistently stopped plausible mistakes from reaching real users. It is not theory or enterprise ceremony. It is how I learned to move fast without shipping on faith.

## The loop

```text
CONTRACT -> INVESTIGATE -> IMPLEMENT -> VERIFY -> REVIEW -> HARDEN -> SHIP
    ^                                                                    |
    |____________________ route findings back ___________________________|
```

Every non-trivial production change follows this loop. The rigor changes with risk; the loop does not. The Build Contract names the proof, investigation validates it, and verification runs it.

## Start here

Use [Quick Ship](playbooks/quick-ship.md) for every normal shipping task. Add [Risky Ship](playbooks/risky-ship.md) only when the change touches:

- Authentication, permissions, security boundaries, or agents taking consequential actions.
- Payments.
- Sensitive user data.
- Database migrations.
- Destructive actions.
- Public APIs or contracts other systems depend on.
- Changes that are difficult to undo.

If none applies, stay in Quick Ship. Risky Ship adds requirements to the same delivery loop; it is not a second process. Add the [Autonomous Loop](playbooks/autonomous-loop.md) when work is recurring, scheduled, unattended, or must continue across context boundaries.

Use the minimum process that prevents avoidable rework. Every artifact, meeting, agent pass, and check must clarify intent, reduce a visible risk, or produce reproducible evidence. If it does none of those things, skip it.

## The ten-minute workflow

```text
1. Challenge whether the problem is real, worth solving, or already solved.
2. Agree the smallest useful Build Contract.
3. Let the agent investigate, establish the baseline, and validate the proof and technical plan.
4. Implement one coherent vertical slice.
5. Run the agreed proof and applicable project checks; exercise the real system where practical.
6. Review in a fresh context.
7. Harden only for the risks that apply.
8. Release, observe, and know how to roll back.
```

### Write the Build Contract

A Build Contract is a short, living agreement about what should change, what must not change, how success will be proved, and how the change can be undone. It is not a legal contract or a full PRD.

Copy this into the agent. One or two lines per field is normally enough.

```text
PROBLEM AND EVIDENCE: Who has the problem, what happens today, and why do we believe it matters?
SMALLEST USEFUL CHANGE: What is the smallest version worth shipping or testing?
CURRENT BEHAVIOR: What happens today? Include when changing existing behavior.
EXPECTED BEHAVIOR: What should the user or system be able to observe?
EXPECTED OUTCOME: What user behavior or business result should change? Optional for non-product work.
WHAT MUST STILL WORK: Which existing behavior must not break?
NON-GOALS: What is deliberately outside this change?
CONTEXT, RISKS, AND CONNECTIONS: Which designs, research, data, constraints, users, workflows, or systems should be inspected?
PROOF: What exact test, walkthrough, screenshot, result, or metric proves the implementation works as intended?
ROLLBACK: How can the change be disabled or undone?
OPEN DECISIONS: What remains unclear, assumed, or human-owned?
```

If implementation changes what “right” means, update the contract and proof before updating the code.

### Ask the agent to investigate

```text
Inspect the existing project before editing anything.

Tell me:
1. Whether this capability already exists.
2. Which patterns and interfaces should be reused.
3. Which risks, ambiguities, or affected workflows I missed.
4. The smallest technical approach you recommend.
5. How you will prove the change works and preserves existing behavior.

Do not implement until consequential questions are resolved.
```

For meaningful logic, proof includes a normal case, an edge case, and a failure case. A bug fix begins by reproducing the bug. “The agent says it works” is not proof.

### Review without implementation loyalty

Use a fresh conversation or independent reviewer. Provide the Build Contract, plan, changes, and evidence.

```text
Review this change against the Build Contract. Be adversarial.

Find logic, security, privacy, permission, scope, preservation,
edge-case, test, and operational problems. List blockers first.
For each finding, identify whether the contract, proof,
implementation, or project constraints must change.
```

Before release, confirm the agreed proof passes, important existing behavior still works, blocking findings are resolved, rollback is known, and someone knows what to observe after release.

## Who owns what

| PM or founder owns | Agent or engineer owns |
| --- | --- |
| Problem, target user, and desired outcome | Repository and system investigation |
| Smallest useful scope and non-goals | Technical options and recommended approach |
| User-visible behavior and business constraints | Implementation plan, tasks, and code |
| Acceptable risk and consequential product decisions | Risk discovery and mitigation options |
| What convincing product proof looks like | Tests, technical checks, and evidence collection |
| Final product judgment and release decision | Clear reporting of results, limits, and residual risks |

The agent may challenge and recommend. It must not silently redefine product intent. Delegating implementation does not delegate accountability.

## Repository map

| File | Load it when |
| --- | --- |
| [GRIT.md](GRIT.md) | You need the canonical principles and operating model |
| [Quick Ship](playbooks/quick-ship.md) | Any product or engineering change that may ship—start here |
| [Risky Ship](playbooks/risky-ship.md) | Add to Quick Ship when a visible risk trigger above applies |
| [Autonomous Loop](playbooks/autonomous-loop.md) | Add when work is recurring, scheduled, unattended, or must continue across context boundaries |
| [Verification](reference/verification.md) | Designing tests, evals, hardening, security gates, or release evidence |
| [Agent Operations](reference/agent-operations.md) | Configuring context, permissions, architecture, Git, dependencies, or parallel agents |

Humans and agents should start with project instructions and a Build Contract, then load only the files in this table whose conditions apply. Anything that may ship starts with Quick Ship.

GRIT grew from the practical failures that appear when plausible AI-generated code meets real users. The framework should keep evolving from observed failures—not from adding ceremony in advance.
