# GRIT

**Guidelines and Rules for Iterating on Things (with AI)**

Lean specs. Failing tests. Fresh contexts. Adversarial review. Verification before trust.

---

## Table of Contents

- [What this is](#what-this-is)
- [Who this is for](#who-this-is-for)
- [What this is not](#what-this-is-not)
- [1. The Loop: Spec -> Test -> Implement -> Review -> Ship](#1-the-loop-spec---test---implement---review---ship)
- [2. Context Hygiene](#2-context-hygiene)
- [3. AI/LLM Coding Patterns](#3-aillm-coding-patterns)
- [4. Architecture Principles](#4-architecture-principles)
- [5. Testing Strategy](#5-testing-strategy)
- [6. Version Control Workflow](#6-version-control-workflow)
- [7. Security Checklist](#7-security-checklist)
- [8. Shipping Rules](#8-shipping-rules)

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
6. **Ship** - only after the change survives verification.

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

## What this is not

GRIT is not:

- A prompt pack.
- A benchmark or model ranking.
- A replacement for engineering judgment.
- A waterfall process with AI stapled onto it.
- A reason to skip human ownership, security review, or product review.
- A heavyweight process for every typo, copy edit, or tiny config change.
- A guarantee that AI-generated code is safe.

The point is not to make AI coding ceremonial. The point is to make it inspectable.

---

## 1. The Loop: Spec -> Test -> Implement -> Review -> Ship

Every non-trivial feature follows this cycle.

```text
SPEC -> TEST -> IMPLEMENT -> REVIEW -> SHIP
  ^                                      |
  |________ route findings back _________|
```

### Step 1: Spec

Write a lean behavioral contract before touching code. This is your real prompt to the AI, not a claim that you already understand every edge case.

A useful spec contains:

- **What it does** - 2-3 sentences in plain English.
- **Inputs and outputs** - data shapes, types, return values, errors.
- **Edge cases** - empty, null, malformed, concurrent, maximum, expired, unauthorized.
- **Does not do** - explicit boundaries that prevent scope creep.
- **Uncertainty** - unresolved decisions marked clearly.

Example:

```text
FEATURE: Order Total Calculator

DOES:
Takes line items and an optional discount code. Returns subtotal,
discount, tax, and final total.

INPUTS:
{ items: LineItem[], discountCode?: string, taxRate: number }

OUTPUTS:
{ subtotal: number, discount: number, tax: number, total: number }

EDGE CASES:
- Empty items array returns all zeros.
- Invalid discount code is ignored.
- Negative quantity returns a validation error.

DOES NOT:
- Persist the order.
- Validate inventory.
- Charge payment.

NEEDS CLARIFICATION:
- Should tax be calculated before or after discount?
```

Resolve every blocking `NEEDS CLARIFICATION` marker before tests. If an open question does not block the first slice, write down the current assumption and continue.

Specs are living hypotheses, not waterfall gates. If implementation reveals a missing case, update the spec, add or revise the tests, then change the code. The spec becomes stronger because you built against it, not because you guessed perfectly upfront.

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

Use a fresh context for implementation when the prior conversation was long, exploratory, or full of failed attempts. Long conversations carry stale assumptions.

### Step 4: Review

Use a fresh context for review. If possible, use a different model family than the one that wrote the code.

Why this helps:

- The reviewer has no loyalty to the implementation.
- Fresh context exposes assumptions the builder carried forward.
- Different models often notice different classes of mistakes.

Reviewer prompt:

```text
Review this implementation against the spec. Be adversarial.

Find:
- Logic bugs and silent failures
- Security issues
- Spec violations
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

### Step 6: Ship

Ship after the code passes tests, checks, and adversarial review. Do not polish what does not need polishing. Do not expand scope at the finish line.

### When to Scale the Process

Rigor should be proportional to risk, complexity, and uncertainty. The loop is fixed; the ceremony is not.

| Situation | Minimum rigor |
| --- | --- |
| Copy, comments, tiny config | Lightweight review |
| UI-only styling or layout | One-line spec, visual check |
| Core logic | Full loop |
| Data model change | Full loop plus migration test |
| External API integration | Full loop plus mock or sandbox test |
| Auth, money, user data | Full loop plus cross-model review |

---

## 2. Context Hygiene

Context quality strongly affects AI code quality. Treat context as a scarce resource.

### Start Fresh for Each Feature

Do not carry a long debugging conversation into a new implementation. Start with:

1. The spec.
2. The failing tests.
3. The relevant existing files or interfaces.

One feature per conversation is a useful default.

### Provide the Right Context

Do not dump the whole codebase into the prompt. Give the AI:

- The spec for this feature.
- The tests it needs to pass.
- The interfaces and types it must obey.
- The small set of files it must integrate with.

More context is not always better. Irrelevant context competes with the task.

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

### Parallelize Carefully

Parallel agents are useful when tasks own different files:

- One agent builds the API route.
- One agent writes tests.
- One agent builds the UI component.

Do not parallelize when agents need to edit the same files, share database migrations, or touch lock files. Merge sequentially, rebase each branch on the latest main, then run the full test suite.

### Make the Repo Legible

Every fresh AI session starts blind. Make orientation cheap.

Keep a root context file such as `AGENTS.md`, `CLAUDE.md`, `.cursorrules`, or `.windsurfrules`. Keep it short and human-written.

Good context file shape:

```markdown
# Project Name

## Commands
- Build: `npm run build`
- Test: `npm run test`
- Lint: `npm run lint --fix`
- Type check: `npx tsc --noEmit`
- Single test: `npx vitest run src/path/to/test.ts`

## Stack
Next.js, TypeScript, Postgres, Drizzle, Vitest.

## Read on demand
- Architecture: `docs/architecture.md`
- Database schema: `src/db/schema/`
- Current progress: `PROGRESS.md`

## Boundaries
- Always run type check and relevant tests before done.
- Ask first before schema migrations, new dependencies, or deleting files.
- Never commit secrets or modify `.env` files.
```

Use the context file as a table of contents, not as a novel. Link to deeper docs instead of loading them into every session.

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

## 3. AI/LLM Coding Patterns

### Prefer Structured Outputs

For LLM calls that return data, use tool calls, JSON mode, function calling, schemas, or another structured-output mechanism. Avoid parsing free text with regex.

```typescript
// Fragile
const score = parseInt(response.content.match(/Score: (\d+)/)?.[1] ?? "0", 10)

// Better
const result = await model.generateObject({
  schema: ScoreSchema,
  prompt,
})
```

Structured outputs fail loudly. Free-text parsing fails sideways.

### Protect Against Prompt Injection

Any feature that processes user-uploaded documents, URLs, web pages, or free text is handling untrusted input.

Rules:

1. Do not interpolate untrusted content into system prompts.
2. Pass user content as separate messages or data fields.
3. Validate LLM outputs before database writes or side effects.
4. Separate read-only analysis from write-capable actions.
5. Log prompts and responses where safe, with secrets and personal data redacted.

```typescript
// Bad
system: `Analyze this document: ${userInput}`

// Better
system: "You are a document analyzer. Analyze the document provided by the user."
messages: [{ role: "user", content: userInput }]
```

### Version Prompts

Store important system prompts in versioned files, not scattered inline strings. Prompt changes are behavior changes. They should be diffable, reviewable, and reversible.

### Choose Models Deliberately

Pick models by capability, latency, cost, and risk:

- Cheap/fast models for high-volume classification, extraction, and simple structured tasks.
- Mid-tier models for routine coding, conversation, and moderate reasoning.
- Strong models for complex architecture, security-sensitive changes, user-facing prose, or ambiguous debugging.

Cache stable results. Set output limits tightly. Batch where it is safe.

### Rules for AI-Generated Code

Bake these into prompts or project guidance:

- Make surgical changes.
- Match the existing style.
- Surface assumptions instead of silently choosing.
- Do not add speculative flexibility.
- Do not refactor unrelated code.
- At system boundaries, validate aggressively.
- Inside trusted internal code, avoid defensive clutter for states that cannot occur.

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

### Verify End to End

AI-generated code often passes unit tests while failing in the actual app. For user-facing changes, run the app and verify the flow with Playwright, browser automation, or equivalent tooling.

The minimum useful loop:

1. Start the dev server.
2. Navigate like a user.
3. Click, type, submit, refresh.
4. Inspect the rendered state.
5. Capture screenshots on failure.
6. Fix before calling the work done.

"The tests pass" is good. "The feature works in the running app" is better.

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

Each agent gets its own directory with its own branch checked out. No stashing, no context-switching, no lock file collisions. When the work is merged, clean up:

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

### Rebase Before Merge

When multiple agents work in parallel, merge sequentially. After each merge, rebase the next branch on the updated main and run the full test suite.

```bash
git checkout feat/team-invitations
git rebase main
npm test
# if green, merge
git checkout main
git merge feat/team-invitations

# now rebase the next branch
git checkout fix/discount-rounding
git rebase main
npm test
```

This catches integration issues one branch at a time instead of discovering them all at once in a broken main.

### Tag What the AI Wrote

Some teams want to know which code was AI-generated. Options, from lightweight to heavy:

- **Commit co-author**: add `Co-Authored-By: AI Agent <agent@tool>` to commit messages. Most tools do this automatically.
- **PR labels**: tag pull requests with `ai-generated` or `ai-assisted`.
- **Branch prefix**: `ai/feat/invitations` makes it visible in `git branch --list`.

Pick one convention and stick with it. The goal is traceability, not ceremony.

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

Automate as much of this as possible. Manual checklists are the fallback, not the foundation.

---

## 8. Shipping Rules

1. Ship small changes.
2. Spec non-trivial behavior before implementation.
3. Test core paths, not cosmetics.
4. Use fresh context for fresh work.
5. Review AI-generated code adversarially.
6. Cross-model review anything touching money, auth, or user data.
7. Route findings back to spec, tests, or code.
8. Do not abstract until the pattern is real.
9. Verify user-facing features in the running app.
10. Keep the repo legible for the next fresh session.
