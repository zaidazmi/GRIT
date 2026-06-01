# GRIT

**Guidelines and Rules for Iterating on Things (with AI)**

Lean specs. Failing tests. Fresh contexts. Adversarial review. Verification before trust.

---

## Table of Contents

- [What this is](#what-this-is)
- [Who this is for](#who-this-is-for)
- [1. The Loop: Spec -> Test -> Implement -> Review -> Harden -> Ship](#1-the-loop-spec---test---implement---review---harden---ship)
- [2. Context Hygiene](#2-context-hygiene)
- [3. Agent Constraints](#3-agent-constraints)
- [4. Architecture Principles](#4-architecture-principles)
- [5. Testing Strategy](#5-testing-strategy)
- [6. Version Control Workflow](#6-version-control-workflow)
- [7. Security Checklist](#7-security-checklist)
- [8. Multi-Agent Orchestration](#8-multi-agent-orchestration)
- [9. Dependency Governance](#9-dependency-governance)
- [10. Feedback Loops](#10-feedback-loops)

---

## What this is

GRIT is a quality-control system for teams that use AI agents to write meaningful parts of their software. It works with Claude Code, Cursor, Codex, Windsurf, or any agentic coding environment that can read files, edit code, run tests, and review changes.

The premise is simple: AI can produce plausible-looking code that quietly fails. The fix is not just a smarter model. The fix is a disciplined loop that makes intent explicit, forces verification, and separates building from reviewing.

GRIT is not an "airtight spec before building" process. Implementation is part of discovery. The first spec is a hypothesis good enough to start, and the loop exists because building teaches you what the spec missed.

The loop:

1. **Spec** - write down the behavior well enough to start.
2. **Test** - create failing tests from the spec.
3. **Implement** - give the AI the spec and tests; its job is to make them pass.
4. **Review** - use a fresh context, ideally with a different model, to attack the result.
5. **Route findings** - send each problem back to the phase that caused it.
6. **Harden** - check non-functional requirements the AI did not add.
7. **Ship** - only after the change survives verification.

The spec is your leverage. A clear spec gives the AI less room to improvise. A vague spec makes you debug assumptions you never meant to approve.

---

## Who this is for

GRIT is for:

- Solo builders and small teams using AI agents for real implementation work.
- Engineers who want faster output without turning code review into faith-based shipping.
- Teams working on products with business logic, user data, payments, auth, agents, integrations, or other failure-prone behavior.
- Leads who need a repeatable workflow for reviewing AI-generated pull requests.
- Skeptical developers who are willing to use AI, but only with guardrails.

It is especially useful when the AI is doing more than autocomplete: creating features, changing data models, wiring integrations, writing tests, or refactoring existing code.

---

## 1. The Loop: Spec -> Test -> Implement -> Review -> Harden -> Ship

Every non-trivial feature follows this cycle.

```text
SPEC -> TEST -> IMPLEMENT -> REVIEW -> HARDEN -> SHIP
  ^                                                |
  |_____________ route findings back ______________|
```

For day-to-day work, choose the lightest mode that still protects the user:

| Mode | Use when | Minimum bar |
| --- | --- | --- |
| **Spike** | Exploring an idea you may throw away | No ceremony, but spike code does not ship until reviewed or rewritten |
| **Quick Ship** | Small or medium changes with clear intent | Compact spec, search first, test or manual proof, fresh review, rollback path |
| **Risky Ship** | Auth, money, user data, migrations, destructive actions, security, or hard-to-undo behavior | Full loop, automated tests, hardening, adversarial review, rollback/monitoring plan |

### Step 0: Challenge the Premise

Before writing a spec, answer these four questions in one line each. Write the answers at the top of the spec file — they take 60 seconds and they prevent the most expensive class of bug: a well-implemented feature that should not exist.

```text
PROBLEM: [What problem does this solve? For whom? How do they solve it today?]
SMALLEST: [Is this the smallest version that delivers value?]
EXISTS: [Does this already exist in the codebase under a different name?]
NULL CASE: [What happens if we do nothing?]
```

If you cannot answer PROBLEM in one sentence, you are not ready to spec. If EXISTS turns up an existing solution, you are done. If NULL CASE is "nothing bad happens," reconsider whether this belongs in the current sprint.

Most of the time the answers confirm "yes, build it" and you move on. The written answers remain in the spec file as context for the next person (or agent) who asks "why does this exist?"

### Step 1: Spec

Write a lean behavioral contract before touching code. This is your real prompt to the AI, not a claim that you already understand every edge case.

Separate the contract into three parts:

- **Intent** - the outcome you want and why it matters.
- **Expectations** - what counts as done, what counts as failed, and what must stay out of bounds.
- **Context** - the existing system facts the agent needs to implement without guessing.

Do not let one sprawling document blur these together. The human owns intent and expectations. The agent can help discover context, but it should not invent what "done" means.

A useful spec contains:

- **Intent** - what outcome this creates for the user or system.
- **Expectations** - success scenarios, failure scenarios, edge cases, and explicit non-goals.
- **Connections** - adjacent workflows, domain rules, APIs, data, or prior decisions this change may touch.
- **Context needed** - files, interfaces, patterns, or constraints the agent must inspect before coding.
- **Uncertainty** - unresolved decisions marked clearly.

For fast changes, a compact spec is enough:

```text
INTENT:
USER VALUE:
EXPECTATIONS:
SUCCESS SCENARIOS:
FAILURE SCENARIOS:
CONNECTIONS:
CONTEXT NEEDED:
DOES NOT DO:
TEST OR MANUAL CHECK:
ROLLBACK:
UNCLEAR:
```

Example:

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

Resolve every blocking unclear item before tests. If an open question does not block the first slice, write down the current assumption and continue.

For existing systems, prefer delta specs over restating the whole product. Describe only what changes:

```text
CURRENT BEHAVIOR:
NEW BEHAVIOR:
WHAT STAYS THE SAME:
CONNECTIONS:
SUCCESS SCENARIOS:
FAILURE SCENARIOS:
ROLLBACK:
```

For risky work, run the hole test before implementation: hand the intent and expectations to a fresh reviewer, or a fresh model context, and ask where the agent would still have to guess. Close those holes before coding. The goal is not to specify the whole system; it is to remove the guesses that would change the outcome.

Specs are living hypotheses, not waterfall gates. If implementation reveals a missing case, update the spec, add or revise the tests, then change the code. The spec becomes stronger because you built against it, not because you guessed perfectly upfront.

The spec is authoritative for the current pass. If you discover it's wrong, stop and update the spec first — don't improvise around it.

When requirements change mid-implementation — a flow reversal, a new constraint, a pivot in approach — stop building. Update the spec first, then update or add tests, then resume implementation. Do not patch code to match new requirements without cascading. An agent that patches without updating the spec leaves the next session with a lie as its starting point.

### Diagram the Spec Before Building

For features with multiple services, state transitions, or non-trivial data flow, ask the agent to produce a diagram from the spec before writing any implementation code. Use text-based formats (Mermaid, D2) that live in the repo alongside the spec.

This is a diagnostic tool, not documentation. A diagram forces the agent to reason about structural relationships — service dependencies, state transitions, data flow direction — before it starts writing code. If the diagram comes back incoherent or incomplete, the spec has gaps. Catching those gaps in a diagram is far cheaper than catching them in a broken integration.

```text
Prompt:
Before implementing, produce a Mermaid diagram from this spec showing
[state transitions / data flow / service interactions].
Do not write any code yet.
```

Useful diagram types by situation:

| Situation | Diagram type | What it reveals |
| --- | --- | --- |
| Multi-step workflows | State machine | Invalid transitions, missing terminal states |
| API integrations | Sequence diagram | Unclear ownership of calls, missing error paths |
| Data model changes | Entity relationship | Broken references, missing constraints |
| Service interactions | Flowchart | Ambiguous branching, circular dependencies |

Skip this for simple CRUD, config changes, or anything where the data flow is obvious. The goal is to surface structural ambiguity in complex specs, not to generate diagrams for their own sake.

### Step 2: Test

Write tests before implementation for any behavior that matters. Tests are the verification layer for code you did not personally write line by line.

At minimum, cover:

- One happy path.
- One edge case.
- One error or boundary case.

```typescript
test("calculates total for valid items with no discount", async () => {})
test("ignores invalid discount code", async () => {})
test("returns an error for negative quantities", async () => {})
```

It is fine to have the AI draft the tests from the spec. You still review them. Watch for tests that only confirm the implementation shape instead of the behavior.

For low-risk UI, copy, or configuration changes, a manual proof can replace an automated test. Write the exact observation before shipping: browser path, CLI command, API request, screenshot, log line, or other evidence that proves the change works.

Prefer vertical slices: one failing test, make it pass, then the next test. For bug fixes, first write the failing test that reproduces the bug. If the bug was never reproduced, the fix is mostly theater.

### Step 3: Implement

Hand the spec and failing tests to the AI.

```text
Here is the spec:
[paste spec]

Here are the failing tests:
[paste tests]

Implement the smallest change that makes these tests pass.
Follow the spec exactly.
Do not add features that are not in the spec.
```

**Search first.** Before writing new code, the agent must search the codebase for existing patterns, utilities, or similar implementations to reuse. Creating duplicate code is the most common agent failure mode. If a helper already exists, use it. If a pattern is already established, follow it.

**One logical change per pass.** Do not ask the agent to build an entire feature in a single shot. Each pass should be one coherent unit — a new endpoint with its types, handler, service, query, and tests counts as one unit even if it touches 12 files. The limit is not file count; it's whether the agent can hold the full change in context without contradicting itself or forgetting to propagate updates. When the scope is too large for the context window, split by vertical slice, not by arbitrary file limits.

Use a fresh context for implementation when the prior conversation was long, exploratory, or full of failed attempts. Long conversations carry stale assumptions.

**Use Codex Goals when the finish line is clear but the path is uncertain.** Goals fit Quick Ship and Risky Ship work such as flaky tests, performance tuning, migrations, bug hunts, benchmarks, and research or audit tasks. Treat a Goal as an execution contract, not a replacement for GRIT.

```text
/goal <INTENT>, verified by <TEST OR MANUAL CHECK>,
while preserving <EXPECTATIONS / DOES NOT DO>.
Use <CONTEXT NEEDED / BOUNDARIES>.
Between iterations, record what changed, what evidence was gathered,
and the next best action. If blocked, stop with attempted paths,
evidence, blocker, and next input needed.
```

**Presence beats approval.** For risky work, do not disappear until the final diff. Stay in the loop at the moments where human judgment matters: intent, expectations, first implementation checkpoint, and review. A late approval on a diff too large to truly read is not the same as ownership.

Ask the agent to pause before irreversible or high-blast-radius moves:

```text
Before changing data models, auth, billing, permissions, migrations,
or public contracts, stop and show the intended change, affected
connections, rollback path, and verification plan.
```

For risky Goals, make that pause rule part of the Goal text itself.

**Know when to bail out.** If the agent needs more than two correction cycles on a single task, stop and diagnose before trying again. Either the task is too large (the agent cannot hold all the moving parts in context), the spec is too vague (the agent is guessing to fill gaps), or the problem is a bad fit for the current model (complex state machines, subtle concurrency, nuanced protocol work). Split the task, enrich the spec, or write the hard part by hand. A third correction cycle almost never produces what the first two failed to.

### Document Decisions Mid-Build

When you make a structural decision during implementation that was not in the original spec — choosing a data structure, picking an integration approach, deciding what NOT to build — write it down. One line: what you decided and why.

Put it in the spec, a decisions section of the progress file, or a comment at the decision point in code. The format does not matter. What matters is that the next agent session can find it without having your conversation history.

Undocumented decisions get relitigated. A future agent will see the code, not understand the tradeoff, and "improve" it back to something worse. The one-line note costs seconds. Rediscovery costs hours.

### Step 4: Review

Use a fresh context for review. Use the strongest model available — review is where reasoning quality matters most. If the strongest model happens to be a different family than the one that wrote the code, that's a bonus, but model strength beats model diversity every time.

Why a fresh context helps:

- The reviewer has no loyalty to the implementation.
- Fresh context exposes assumptions the builder carried forward.

Reviewer prompt:

```text
Review this implementation against the spec. Be adversarial.

Find:
- Logic bugs and silent failures
- Security issues
- Spec violations
- Implementation that passes tests but drifts from the agreed intent
- Missing edge cases
- Weak or tautological tests
- Unnecessary scope added by the implementation

Return findings by severity. For each finding, say whether it belongs
in the spec, tests, or implementation.

[paste spec + tests + implementation]
```

Review the plan first, then inspect the diff against the plan. A good plan explains intent. The diff proves whether the code actually followed it.

### Step 5: Route Findings

Do not just patch the symptom. Trace the problem to the phase that created it. This does not mean restarting the whole process for every discovery. Small findings should produce small updates to the spec, tests, or code.

| Finding | Route back to |
| --- | --- |
| Spec is ambiguous or missing cases | Step 1: fix the spec, then cascade |
| Tests miss a real scenario | Step 2: add the test, then re-implement |
| Implementation violates the spec | Step 3: fix the code |
| Implementation works but the spec was wrong | Step 1: update the spec, then cascade |

A spec bug fixed only in code will come back later.

### Step 6: Harden

AI reliably produces the functional 80% of a feature but misses non-functional requirements almost every time. Review catches logic bugs and spec violations. NFRs need their own pass because the AI will not add what you did not ask for.

After implementation passes review, run a hardening pass. Not every item applies to every change — use judgment.

**Quick checks** (configuration-level, minutes to add):

- [ ] Rate limiting on new endpoints.
- [ ] Timeout configuration for async operations.
- [ ] Input sanitization at system boundaries.
- [ ] Resource cleanup (connections, file handles, subscriptions).
- [ ] Audit logging for state-changing operations.

**Design checks** (require thought, may require rework):

- [ ] Retry logic with backoff for external calls.
- [ ] Graceful degradation when dependencies fail.
- [ ] PII handling — is sensitive data logged, cached, or exposed?
- [ ] Idempotency for operations that may be retried.
- [ ] Concurrency safety — race conditions, deadlocks, duplicate processing. This is not a checkbox. If your feature has concurrent access to shared state, treat this as a design problem, not a line item.

This can be a separate agent pass or a human review. Automate what you can with linters and static analysis. When a production incident traces back to a missing NFR, add it to the checklist. The checklist grows from failure, not from imagination.

### Step 7: Ship

**Verify before declaring done.** The agent cannot call work complete without proof. At minimum:

1. All tests pass (run them, do not assume).
2. Type checks pass.
3. Linter passes.
4. For user-facing changes: the feature works in the running app (not just in tests).
5. For changes that affect users, data, billing, auth, or availability: the rollback path is known.
6. After deploy: observe the relevant path, logs, metrics, or error tracker long enough to catch obvious breakage.

No optimistic declarations of success. "I believe this should work" is not verification. "Tests pass, I checked the UI, here is what I confirmed" is verification.

Ship after the code passes tests, checks, and adversarial review. Then do a short post-ship observation pass. Do not polish what does not need polishing. Do not expand scope at the finish line.

### When to Scale the Process

Rigor should be proportional to risk, complexity, and uncertainty. The loop is fixed; the ceremony is not.

| Situation | Minimum rigor |
| --- | --- |
| Copy, comments, tiny config | One-shot check |
| UI-only styling or layout | Compact spec, visual check |
| Core logic | Quick Ship or full loop |
| Data model change | Full loop plus migration test |
| External API integration | Full loop plus mock or sandbox test |
| Auth, money, user data | Full loop plus cross-model review |

---

## 2. Context Hygiene

Context quality affects code quality. Treat context as scarce.

### Start Fresh for Each Feature

Do not carry a long debugging conversation into a new implementation. Start with:

1. The spec.
2. The failing tests.
3. The relevant existing files or interfaces.

One feature per conversation is a useful default.

### Provide the Right Context

Do not dump the whole codebase into the prompt or mix all context into the intent. Give the AI:

- The spec for this feature.
- The tests it needs to pass.
- The interfaces and types it must obey.
- The small set of files it must integrate with.

More context is not always better. Irrelevant context competes with the task. Let the agent search for implementation context progressively, then summarize what it found before editing.

### Watch for Context Degradation

Reset when the agent starts repeating itself, over-explaining, apologizing, or adding convoluted fixes. Also reset after several failed correction cycles. At that point the conversation often contains more failure history than useful direction.

When a session gets long, ask the agent to produce a short handoff file:

- Key decisions.
- Current state.
- Files changed.
- Tests run.
- Remaining risks.
- Exact next step.

Review that handoff yourself, then continue in a fresh context.

### Use Sub-Agents for Exploration

Sub-agents are useful for protecting context. Let a side agent inspect a large code path, then return a compressed summary to the main thread.

Example:

```text
Main task: Add team invitations.

Sub-agent task:
Find how auth, roles, and email sending work in this repo.
Return the 5-10 facts needed for implementation, with file paths.
Do not edit files.
```

The main agent stays focused on the feature instead of carrying every exploratory file read.

### Make the Repo Legible

Every fresh AI session starts blind. Make orientation cheap.

Keep a root context file such as `AGENTS.md`, `CLAUDE.md`, `.cursorrules`, or `.windsurfrules`. Keep it short and human-written.

Good context file shape: commands (build, test, lint), stack, pointers to deeper docs, and boundaries (what to ask before doing). Keep it under 50 lines. Use it as a table of contents, not a novel — link to deeper docs instead of inlining them.

Good file names help too. A tree like `src/agents/router.ts`, `src/db/schema/users.ts`, and `src/prompts/onboarding.ts` teaches architecture faster than a paragraph that can go stale.

### Keep a Progress File

For long-running work, maintain a small `PROGRESS.md`:

```markdown
## Current Sprint: Team Invitations

### Completed
- [x] Invitation table
- [x] API route for creating invites

### In Progress
- [ ] Accept-invite flow

### Known Issues
- Email preview only works in development
```

The next session should not have to rediscover what happened.

---

## 3. Agent Constraints

### Severity Hierarchy

Structure agent rules by severity so the agent knows what it can do freely, what requires a human gate, and what is never acceptable. Use this hierarchy in your rules file (CLAUDE.md, .cursorrules, AGENTS.md, or equivalent).

**ALWAYS — do these without asking:**

- Run lint and affected tests before committing.
- Match the existing code style and patterns.
- Search the codebase before creating new files or utilities.
- Make surgical changes — touch only what was asked.
- At system boundaries, validate aggressively. Inside trusted internal code, avoid defensive clutter for states that cannot occur.

**ASK FIRST — stop and get human approval:**

- Database schema changes or migrations.
- Adding new dependencies.
- Changing auth logic, permissions, or access control.
- Modifying API contracts that other services consume.
- Any architectural decision not covered by the spec.

**NEVER — these are non-negotiable, enforce in hooks and CI:**

- **Do not improve adjacent code.** Do not "clean up" surrounding code, comments, or formatting. Orthogonal damage is the #1 source of unexpected regressions from agents.
- **Do not suppress errors.** Never catch an exception and silently continue. Never replace an error with a default value unless the spec explicitly requires it. Suppressed errors become production mysteries.
- **Do not create files unless necessary.** Prefer editing existing files. New files mean new imports, new test files, new mental overhead. Justify every new file.
- **Do not hallucinate imports or APIs.** If you are unsure whether a function, module, or package exists, search for it. Do not guess and hope it compiles.
- **Do not exceed scope.** If the spec says "add a discount field," do not also add a coupon system, a discount history table, and an admin UI for managing discounts.
- **Do not guess. Ask.** When you encounter ambiguity — an unclear requirement, an architectural decision with multiple valid paths, an interface you are unsure exists — stop and ask. A wrong guess costs more than the interruption of asking.
- **Do not commit .env files, secrets, or credentials.** Do not skip type checks or linter passes.

### Constrain Agents Programmatically

The harness around an agent matters more than the prompt inside it. Treat agent permissions like you'd treat a service account.

- Least privilege: scope what files, APIs, and tools each agent can touch.
- Human gates on irreversible actions (DB writes, deployments, deletions).
- Structured outputs for all tool calls. No free-text action parsing.
- Explicit allowlists for which tools and APIs an agent can invoke.
- Deterministic workflow engines for sequencing. Do not let agents decide execution order for multi-step operations.

A well-constrained agent with a mediocre prompt is safer than an unconstrained agent with a perfect one.

---

## 4. Architecture Principles

### Pure Core, Effectful Shell

Separate business logic from side effects where the boundary is real.

- Pure core: deterministic functions, data in and data out.
- Effectful shell: database calls, APIs, queues, LLM calls, browser automation.

```typescript
export function calculateTotal(items: LineItem[], discountCode?: string): Total {
  return { subtotal, discount, tax, total }
}

export async function processOrder(orderId: string) {
  const order = await db.getOrder(orderId)
  const total = calculateTotal(order.items, order.discountCode)
  await db.saveTotal(orderId, total)
}
```

This makes AI-generated code easier to test and harder to entangle.

Treat the boundary as provisional while the feature is still being discovered. Do not pretend a messy dependency is pure just to preserve a diagram. Move the boundary when implementation proves the original shape was wrong.

### Do Not Abstract Too Early

Write the pattern a few times before extracting a utility. Premature abstraction is especially expensive with AI because the model tends to overgeneralize from weak signals.

### Enforce Rules Programmatically

Documentation is not enough. Add checks that fail before bad code lands.

Baseline:

```bash
npx tsc --noEmit
npx eslint src/ --max-warnings 0
npx vitest run --reporter=dot
```

Use pre-commit hooks or CI. For larger codebases, enforce module boundaries with ESLint import rules, dependency-cruiser, or similar tools.

Example boundary rules:

```text
src/api/     can import from src/domain/, src/db/, src/auth/
src/domain/  cannot import from src/db/ or external services
src/prompts/ cannot import runtime code
src/db/      cannot import from src/api/
```

The AI cannot reliably remember architecture across sessions. Your tooling can.

---

## 5. Testing Strategy

### Test From the Core Outward

Priority order:

1. Core data structures and pure operations.
2. Scoring, matching, ranking, pricing, permissions, and other business rules.
3. API routes and validation.
4. Async orchestration, queues, retries, and failure states.
5. External integrations using fixtures, mocks, sandboxes, or occasional real tests.
6. User-facing flows with E2E tests.

### Do Not Waste Tests

Usually skip tests for:

- Pure styling and layout.
- Third-party library internals.
- Database driver behavior.
- SDK internals for auth or payments.

Test your usage of those systems, not the systems themselves.

### Test LLM Outputs by Shape

Do not assert exact prose. Assert schema, bounds, and sanity.

```typescript
test("returns valid structured output", async () => {
  const result = await scoreCandidate(mockInput)

  expect(result.score).toBeGreaterThanOrEqual(0)
  expect(result.score).toBeLessThanOrEqual(100)
  expect(result.reasons.length).toBeGreaterThan(0)
  expect(result.reasons.every((r) => typeof r === "string")).toBe(true)
})
```

---

## 6. Version Control Workflow

AI agents generate code fast. Without a disciplined git workflow, that speed turns into merge conflicts, mystery commits, and branches that nobody can explain next week.

### One Branch Per Feature, One Agent Per Branch

Each non-trivial AI task gets its own branch. Name it descriptively: `feat/team-invitations`, `fix/discount-rounding`, not `ai-changes-3`.

```bash
git checkout -b feat/team-invitations main
```

The agent works on this branch. When it is done, the branch goes through review before merging. No committing AI output directly to main.

### Use Git Worktrees for Parallel Agents

When running multiple agents in parallel, use git worktrees instead of separate clones or risky branch-switching. Worktrees let multiple agents work on different branches simultaneously from the same repo, without stepping on each other.

```bash
git worktree add ../project-feat-invitations feat/team-invitations
git worktree add ../project-fix-discount fix/discount-rounding
```

Each agent gets its own directory with its own branch checked out. No stashing, no context switching, no lock file conflicts. When the work is merged, clean up:

```bash
git worktree remove ../project-feat-invitations
```

Tools like Claude Code support worktrees natively. Use them. Running two agents in the same working directory on different branches is asking for corrupted state.

### Commit Hygiene

AI agents tend to produce either one giant commit or dozens of meaningless ones. Neither is useful.

Rules:

- One logical change per commit.
- Commit messages describe *why*, not *what*. The diff already shows what.
- If the agent made a mess of the history, squash before merging: `git rebase -i main` on the feature branch.
- Never force-push to shared branches. Force-push to your own feature branches only.

Good:

```text
fix: apply discount before tax calculation

The spec requires discount to reduce the taxable amount.
Previously tax was calculated on the full subtotal.
```

Bad:

```text
update code
fix stuff
wip
```

### Tag What the AI Wrote

Mark AI-generated code for traceability: commit co-author lines, PR labels (`ai-generated`), or branch prefixes (`ai/feat/invitations`). Pick one convention and stick with it.

### Do Not Let Agents Push Directly

AI agents should commit to local branches. A human reviews, rebases if needed, and pushes. If your CI runs on push, an agent pushing untested code will break the build for everyone.

The exception is when you have a CI gate that blocks merge without passing checks and a human still approves the PR. In that case, the agent can push to a feature branch, but never to main.

---

## 7. Security Checklist

Before launch, and after any auth, payment, data model, or agent behavior change, verify:

### Auth and Access

- [ ] API routes check authentication.
- [ ] Users can access only their own data.
- [ ] Authorization checks happen server-side.
- [ ] Webhooks verify signatures.
- [ ] Production CORS is not wildcarded.

### AI-Specific

- [ ] Untrusted content is never interpolated into system prompts.
- [ ] LLM outputs are validated before writes or actions.
- [ ] Browser agents run in isolated environments.
- [ ] Tool calls are allowlisted by capability.
- [ ] Logs redact secrets and sensitive personal data.

### Standard Web

- [ ] Public endpoints have rate limits.
- [ ] Environment variables are not exposed to the client.
- [ ] File uploads validate type, size, and storage path.
- [ ] Raw SQL uses parameterized queries.
- [ ] Error messages do not leak internal details.

### Automated Security Gates

Manual checklists are the fallback, not the foundation. Automate what you can.

Minimum CI pipeline for AI-authored code:

```bash
# Static analysis (catches XSS, injection, insecure patterns)
semgrep --config=auto src/

# Secret scanning (catches hardcoded API keys, tokens, credentials)
trufflehog filesystem --directory=. --only-verified

# Dependency audit (catches known-vulnerable packages)
npm audit --audit-level=high

# Type checking (catches shape errors the agent introduced)
npx tsc --noEmit
```

These run on every PR, not as a pre-launch ritual. If any gate fails, the PR does not merge. No exceptions for "I'll fix it later."

The manual checklist above remains useful for design-level review (CORS policy, auth architecture, data access patterns) that static tools cannot catch.

---

## 8. Multi-Agent Orchestration

Parallel agents are fast but dangerous without coordination. Agents must not edit the same files, and integration must be verified after the pieces come together.

### Coordination Rules

- Parallel agents must touch different files. Same-file edits require sequential work.
- Use git worktrees, not branch switching, for parallel work. Each agent gets its own directory.
- Limit to 6-10 parallel agents per developer before coordination overhead exceeds gains.
- Merge sequentially: rebase each branch on updated main, run the full suite, then merge the next.

### Task Decomposition Pattern

For features that span multiple services or layers:

```text
1. Architecture agent: plans the cross-cutting change, assigns file ownership per agent.
2. Implementation agents: each owns a vertical slice (API, worker, UI, etc.).
3. Integration agent: wires the pieces together after individual slices pass tests.
4. Review agent: fresh context, adversarial, sees the full assembled picture.
```

The architecture agent produces a plan. Implementation agents receive only their slice of the plan plus the interfaces they must obey. The integration agent reconciles.

### Database Migrations With Agents

Migrations are the highest-risk parallel operation. Rules:

- Migration agent runs before implementation agents. Schema must exist before code that uses it.
- Columns added as nullable first. Never add NOT NULL without a default on existing tables.
- Indexes created concurrently where supported.
- Data backfilled in batches in a separate step, not inside the migration itself.
- One migration per branch. Multiple migrations in a single branch create rebase hell.

### When Not to Parallelize

Do not parallelize when:

- Agents need to edit the same files.
- Work requires shared database migrations.
- Lock files will conflict (package managers, code generators).
- The feature shape is still being discovered. Parallelize execution, not exploration.

---

## 9. Dependency Governance

AI agents add packages freely. Without guardrails, your dependency tree bloats and your attack surface grows.

### Rules

- New dependencies require justification in the PR description. "It was convenient" is not justification.
- Prefer stdlib or existing dependencies over new packages.
- No packages with fewer than 1,000 weekly downloads unless manually vetted for security and maintenance.
- Lock file changes get a dedicated review pass.
- Agents must not add dependencies that duplicate capabilities already in the project.

### Enforcement

```bash
# CI check: flag new dependencies
git diff main -- package.json | grep '"+' | grep -v '"version"'
```

Options for stronger enforcement:

- Maintain an allowlist of pre-approved packages for common needs.
- Use tools like Socket, Snyk, or npm audit in CI to block known-vulnerable additions.
- Require a second human approval for any PR that adds a new dependency.

Before accepting any new package, verify: it does not duplicate existing capabilities, it is actively maintained, its transitive dependency count is reasonable, and it has no known vulnerabilities. If an existing dependency already covers the need, reject the addition.

---

## 10. Feedback Loops

A static methodology rots. GRIT stays useful only if it evolves from real failures, not from hypothetical best practices.

### Rules: Recurring Mistakes Become Constraints

If an agent makes the same class of mistake twice, do not just fix the code. Update your rules file (CLAUDE.md, .cursorrules, AGENTS.md, or equivalent) so the agent cannot make that mistake again. The fix is upstream, not downstream.

```text
1. Review finds a problem (e.g., agent added a raw SQL query without parameterization).
2. Fix the code in this PR.
3. Check: has this type of mistake happened before?
4. If yes: add an explicit rule to the project rules file.
5. If the rule cannot be enforced by text alone: add a linter rule, pre-commit hook, or CI check.
```

### Intent Drift

Intent drift is the gradual divergence between what you meant to build and what the code actually does. It is not new to software — but AI agents accelerate it dangerously.

When a spec is incomplete, the agent fills gaps with statistically likely defaults from its training data. These defaults are plausible for generic systems but may be wrong for your specific business rules. The result is code that passes tests, looks correct in review, and quietly does the wrong thing in production.

Intent drift is hardest to catch because the code is not broken — it is confidently misaligned.

Signs that intent drift is happening:

- Review keeps finding behavior the spec never mentioned. The agent invented it.
- Users report "it works but it's not what I expected" after launch.
- A feature passes all tests but a manual walkthrough reveals wrong defaults, missing constraints, or inverted logic.
- A new agent session "improves" an earlier decision because the rationale was never written down.

The fix is upstream. If review consistently finds behavior the spec did not specify, your specs are not detailed enough on boundaries. If undocumented decisions keep getting relitigated, your decision log is missing entries. Intent drift is a spec quality problem, not an implementation problem.

### Specs: Track What Works

Not all specs produce good first-pass implementations. Pay attention to which spec formats, levels of detail, and boundary definitions consistently give the agent what it needs on the first try. When a spec produces a clean implementation with no review findings, note what made it work. When a spec produces garbage, note what was missing.

Over time you will learn your project's "spec minimum" — the amount of detail below which the agent guesses wrong. This threshold is different for every codebase and every type of change.

### Process: Measure Whether It's Working

Track at least informally:

- How often do review findings route back to a spec problem vs. an implementation problem? If most findings are spec bugs, your specs need more rigor. If most are implementation bugs, your constraints or context may be insufficient.
- How many correction cycles does a typical feature take before it ships? If the number is rising, something upstream is degrading.
- Which review findings recur across projects? These are candidates for permanent additions to your rules file or CI pipeline.

You do not need a dashboard. A monthly 10-minute look at "what kept breaking and why" is enough. The goal is to notice patterns before they become permanent tax.

### Rules File Hygiene

Add: constraints the agent violates repeatedly, patterns it does not discover on its own, boundaries it crosses under ambiguity. Skip: one-time contextual mistakes, style preferences a formatter handles, anything tests already catch.

Keep rules files under 500 lines. Split into directory-scoped files if they grow past that. Review monthly — remove stale rules, promote recurring ones to automated checks.
