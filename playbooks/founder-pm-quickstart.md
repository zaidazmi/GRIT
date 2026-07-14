# Founder/PM Quickstart

Use this guide when you want to ship a feature with an AI coding agent without reading the whole GRIT framework. It is designed for product managers, founders, indie hackers, and fast-moving startup teams.

Most small and medium changes should take about ten minutes to frame before the agent starts building. The goal is not more documentation. The goal is less rework and fewer plausible-looking mistakes.

## The short version

```text
1. Decide whether the problem is worth solving.
2. Describe the smallest useful change and what must not change.
3. Decide what visible evidence would prove it works.
4. Let the agent investigate and propose the technical plan.
5. Build one small, complete slice.
6. Review it in a fresh context.
7. Release, watch the result, and know how to undo it.
```

## First, choose the safe path

Start every shipping task with [Quick Ship](quick-ship.md).

Open [Risky Ship](risky-ship.md), with engineering or specialist help where needed, only if the change touches:

- Authentication or permissions.
- Payments.
- Sensitive user data.
- Database migrations.
- Destructive actions.
- Public APIs or contracts other systems depend on.
- Changes that are difficult to undo.

Use the [Autonomous Loop](autonomous-loop.md) as well when the agent will work unattended, repeatedly, on a schedule, or across multiple sessions.

If none applies, remain in Quick Ship. If investigation reveals a trigger, pause and switch before implementation.

## Startup speed principle

Use the minimum process that prevents avoidable rework. Every artifact, meeting, agent pass, and check must clarify intent, reduce a visible risk, or produce reproducible evidence. If it does none of those things, skip it. Agent speed matters only when it reduces time to an accepted outcome—not when it merely generates more code faster.

## 1. Write the build brief

Copy this into your agent and answer it in plain language. One or two lines per field is normally enough.

```text
PROBLEM:
Who has the problem, and what happens today?

SMALLEST USEFUL CHANGE:
What is the smallest version worth shipping or testing?

USER-VISIBLE BEHAVIOR:
What should the user be able to see or do?

MUST STILL WORK:
What existing behavior must not break?

DOES NOT INCLUDE:
What is deliberately outside this change?

SUCCESS PROOF:
What exact walkthrough, screenshot, result, or test would convince me it works?

RISKS OR QUESTIONS:
What am I unsure about? Could this affect money, access, data, integrations, or recovery?

ROLLBACK:
How can we turn it off or return to the previous behavior?
```

You own the problem, desired behavior, boundaries, and success proof. Do not let the agent silently replace them with what is easiest to implement.

## 2. Ask the agent to investigate before building

Give the agent the build brief and say:

```text
Inspect the existing project before editing anything.

Tell me:
1. Whether this capability already exists.
2. Which existing patterns and files are relevant.
3. Any important risk, ambiguity, or affected workflow I missed.
4. The smallest technical approach you recommend.
5. How you will prove the change works and existing behavior still works.

Do not begin implementation until consequential questions are resolved.
```

You do not need to design the architecture yourself. You do need to question a plan you cannot connect back to the user problem or verify afterward.

## 3. Agree on proof before code

Ask what would fail before the change and pass afterward. Depending on the feature, proof may be:

- A short user walkthrough in the running product.
- A before-and-after screenshot or video.
- An automated test for important behavior.
- A specific API response, database state, log, or metric.
- Repeated example cases for non-deterministic AI behavior.

For meaningful logic, require at least one normal case, one edge case, and one failure case. For a bug, reproduce the bug before accepting the fix.

“The agent says it works” is not proof.

## 4. Build the smallest complete slice

Ask the agent to implement one usable path from start to finish. Avoid asking it to build a large system in one pass.

During implementation:

- Keep the work inside the agreed brief.
- Reuse existing project patterns before adding new systems or dependencies.
- Stop for decisions involving product intent, money, access, sensitive data, public contracts, or irreversible actions.
- Update the brief and proof if implementation reveals that the original idea was incomplete.

If two correction attempts produce no meaningful new evidence, stop patching. Ask whether the problem is the brief, the proof, the technical approach, or missing context.

## 5. Review in a fresh context

Open a new agent conversation or use an independent reviewer. Provide the brief, the proposed approach, the changes, and the evidence.

```text
Review this change as a skeptical product and engineering reviewer.

Check:
- Does it solve the stated user problem?
- Did it change anything outside the agreed scope?
- What user, edge, failure, security, privacy, or operational case is missing?
- Is the evidence independent and strong enough for the claims being made?
- Is the change understandable, reversible, and ready to release?

List blockers first. Do not fix anything until the findings are reviewed.
```

Fresh review matters because the agent that built the solution is naturally biased toward its own approach and context.

## 6. Ship and watch

Before release, confirm:

- [ ] The agreed proof passes.
- [ ] Important existing behavior still works.
- [ ] No unresolved high-impact question is being treated as an assumption.
- [ ] You know how to disable or undo the change.
- [ ] Someone knows what to watch after release.

For a meaningful product change, decide the user or business signal you expect and how long you will observe it. Correct software can still be the wrong product decision.

## Who owns what

| PM or founder owns | Agent or engineer owns |
| --- | --- |
| Problem, target user, and desired outcome | Repository and system investigation |
| Smallest useful scope and non-goals | Technical options and recommended approach |
| User-visible behavior and business constraints | Implementation plan, tasks, and code |
| Acceptable risk and consequential product decisions | Risk discovery and mitigation options |
| What convincing product proof looks like | Tests, technical checks, and evidence collection |
| Final product judgment and release decision | Clear reporting of results, limits, and residual risks |

Delegating implementation does not mean delegating intent or accountability.

## Plain-language GRIT

| Term | Meaning |
| --- | --- |
| Contract | The short agreement about what should change, what should not, and how success is proved |
| Baseline | Evidence of how the product behaves before the change |
| Preservation test | Proof that important existing behavior still works |
| Invariant | Something that must always remain true |
| Eval | Repeatable examples used to judge non-deterministic AI behavior |
| Rollback | How to disable or undo the release |
| Trust boundary | A point where data or authority passes between users, systems, or organizations |
| Fresh context | A new conversation or reviewer without loyalty to the implementation history |

When you need the full method, continue to [GRIT.md](../GRIT.md). For normal feature work, use the complete [Quick Ship playbook](quick-ship.md).
