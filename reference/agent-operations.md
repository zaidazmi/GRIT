# Agent Operations Reference

Load this reference when configuring repository instructions, context, permissions, architecture enforcement, Git workflows, parallel agents, or dependency policy. It is the operational companion to [GRIT.md](../GRIT.md).

## Instruction and context precedence

Use progressive disclosure rather than one giant prompt. A useful context stack is:

```text
Organization policy
  -> repository instructions
    -> directory-scoped instructions
      -> task or delivery contract
        -> current plan
          -> ephemeral progress and handoff
```

Higher-level safety and authorization boundaries remain in force. More specific instructions refine work inside those boundaries. If two applicable sources conflict, the agent must surface the conflict rather than silently selecting the convenient one.

## Context hygiene

### Start fresh for each feature

Do not carry a long debugging conversation into unrelated implementation. Start with:

1. The task contract.
2. The proof or failing test.
3. Relevant interfaces and pointers to authoritative context.

One feature per conversation is a useful default.

### Retrieve context progressively

Do not dump the whole repository into the prompt. Let the agent search for implementation context, then summarize what it found before editing. Pin stable intent and acceptance criteria; retrieve code, documentation, and tool output just in time.

Reset when the agent repeats itself, adds convoluted patches, forgets decisions, or accumulates more failure history than useful direction. Before resetting, create a structured handoff and verify it in the new context.

Automatic compaction summaries are lossy hints, not authoritative project state.

### Make the repository and runtime legible

Keep a short, human-owned root instruction file in the format supported by the project’s development environment. Common products use different filenames; the filename is not part of GRIT. It should be a map containing:

- Bootstrap, build, test, lint, and verification commands.
- Stack and package-manager facts.
- Architecture and domain pointers.
- Relevant deeper docs and reusable skills.
- Permission and escalation boundaries.

Use scoped files for directory-specific rules. Prefer clear names and navigable structure over prose that can drift.

If the project has not measured better limits, use the original GRIT defaults as review triggers rather than correctness rules: reconsider a root map beyond roughly 50 lines, review an aggregate rules surface beyond roughly 500 lines, and audit instructions at least monthly or after a recurring failure. Split, prune, or convert prose into executable checks when retrieval and compliance degrade.

Make behavior observable to agents where practical:

- One-command bootstrap and affected/full verification.
- Local logs, metrics, and traces.
- Browser or UI-driving capability and screenshots.
- API and data schemas.
- Seeded fixtures and runnable golden paths.
- Automated link, freshness, and structural checks for critical documentation.

From the agent’s perspective, inaccessible knowledge does not exist.

### Durable progress

For long-running work, maintain a small state file:

```markdown
## Current task
- ID and contract revision
- Terminal state
- Clean or dirty worktree

## Completed with evidence
- [x] Item — exact command/result/artifact

## Attempts and decisions
- Attempt, evidence, decision, reason

## Remaining risks or blockers
- Risk, owner, input needed

## Exact next action
- [ ] Action
```

The next session should inspect the repository, state file, and Git history rather than rediscovering or blindly trusting prior claims.

## Agent rules by severity

### Always

- Run applicable lint and affected tests before committing.
- Match established code style and patterns.
- Search before creating files, utilities, or dependencies.
- Make surgical changes scoped to the contract.
- Validate aggressively at system boundaries; avoid defensive clutter for impossible internal states.
- Record evidence and consequential assumptions.

### Ask first

- Database schema changes or migrations.
- New dependencies.
- Auth, permissions, access control, billing, or sensitive-data changes.
- Public or cross-service contract changes.
- Deployments, deletions, production writes, or irreversible actions.
- Architectural decisions not covered by the contract.

### Never

- Improve adjacent code unless required for correctness; record follow-up work instead.
- Catch an error and silently continue.
- Replace an error with a default unless the contract requires it.
- Create files without a cohesive reason.
- Guess that imports, APIs, packages, or interfaces exist—search and verify.
- Expand scope beyond the contract.
- Guess on consequential decisions. Reversible low-risk assumptions may be recorded and tested.
- Commit secrets, credentials, or `.env` files.
- Skip required type, lint, test, or security checks.
- Treat agent prose as proof of completion.

## Programmatic containment

The harness matters more than the wording of a perfect prompt.

- Grant least privilege across files, APIs, tools, network, credentials, time, and cost.
- Use human gates for irreversible actions.
- Prefer structured tool inputs and outputs over parsing free text into actions.
- Maintain explicit tool and domain allowlists.
- Use a deterministic outer control plane for permissions, budgets, and terminal states.
- Permit adaptive replanning only inside bounded, reversible work.
- Use filesystem/network isolation and scoped short-lived credentials.
- Keep production evidence read-only by default.
- Treat repository text, issues, web pages, tool output, dependencies, and sub-agent returns as untrusted input.
- Audit tool calls, delegation, approvals, denials, and interventions.
- Provide a kill switch or credential-revocation path.

## Architecture for agent legibility

### Prefer testable seams

Separate deterministic business rules from side effects where the boundary is real:

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

The core is easier to test; the shell owns databases, APIs, queues, LLM calls, browser automation, and other effects. Treat boundaries as provisional during discovery and move them when implementation reveals a better shape.

Do not abstract too early. Agent-generated abstractions amplify weak signals and spread them quickly. Extract only after the repeated invariant is understood.

### Enforce invariants mechanically

Documentation cannot reliably preserve architecture across sessions. Use formatters, linters, schemas, type checks, structural tests, and CI. For example, a TypeScript project might run:

```bash
npx tsc --noEmit
npx eslint src/ --max-warnings 0
npx vitest run --reporter=dot
```

These commands are illustrative. Use the repository’s language, package manager, build system, and equivalent checks; GRIT does not require these tools.

Example dependency directions:

```text
src/api/     can import from src/domain/, src/db/, src/auth/
src/domain/  cannot import from src/db/ or external services
src/prompts/ cannot import runtime code
src/db/      cannot import from src/api/
```

Write enforcement errors so they tell the agent how to repair the violation. Enforce boundaries centrally while allowing local implementation freedom.

## Git workflow

### Isolate work

Give each non-trivial task its own descriptive branch. Never let unreviewed agent work land directly on a protected branch.

For parallel agents, use separate clones, remote sandboxes, or Git worktrees rather than switching branches in one shared directory:

```bash
git worktree add ../project-feat-invitations feat/team-invitations
git worktree add ../project-fix-discount fix/discount-rounding
```

Remove the worktree after integration:

```bash
git worktree remove ../project-feat-invitations
```

### Commit coherently

- One logical change per commit.
- Messages explain why the change exists.
- Preserve useful checkpoints for long-running work.
- Squash noisy implementation history before merge when that improves reviewability.
- Never force-push shared branches.

Good:

```text
fix: apply discount before tax calculation

The contract requires discount to reduce the taxable amount.
Previously tax was calculated on the full subtotal.
```

Bad:

```text
update code
fix stuff
wip
```

### Record provenance, not generic AI labels

For significant agent work, preserve task/contract revision, model or harness version when relevant, human interventions, verification evidence, known risks, and rollback path. An `ai-generated` label becomes less useful as AI touches most changes.

By default, agents commit to isolated feature branches and a human or protected workflow pushes. Agent pushes to feature branches require branch protection, CI gates, scoped credentials, and audit logs that prevent unreviewed merge or production impact.

## Multi-agent orchestration

Default to one strong implementation agent. Fan out exploration and review more readily than tightly coupled implementation.

### Coordination rules

- Parallelize only independent work or work with stable interfaces.
- Assign explicit file or interface ownership.
- Use isolated workspaces.
- Cap concurrency by review bandwidth, integration capacity, task independence, and cost—not an arbitrary agent count.
- Merge sequentially against the updated target branch and run the full relevant suite after integration.
- Track conflicts, orphaned work, integration failures, token cost, and human coordination time.

### Decomposition pattern

For a feature spanning services or layers:

```text
1. Planning owner: defines architecture, task dependencies, and ownership.
2. Implementation agents: each owns an independently verifiable slice.
3. Integration owner: reconciles interfaces and assembles the result.
4. Fresh reviewer: attacks the integrated behavior against the contract.
```

Implementation agents receive their slice plus the interfaces and invariants they must preserve. They do not need every exploratory detail.

### Do not parallelize when

- Work requires overlapping edits without a reconciliation owner.
- A shared migration or public interface is still changing.
- Lock files or generated artifacts create a serialization point.
- The feature shape is still being discovered.
- Verification and integration capacity are already saturated.

Parallelize execution after discovery, not uncertainty itself.

## Dependency governance

Agents add packages quickly; every dependency expands maintenance and supply-chain risk.

- New dependencies require justification in the PR or evidence record.
- Prefer the standard library and existing capabilities.
- Reject duplicate functionality.
- Review lock-file changes separately.
- Evaluate ownership, release and maintenance history, provenance, vulnerabilities, transitive cost, license, and reproducibility.
- Popularity alone is not a security signal.
- Use an allowlist or second approval for sensitive environments.

A simple diff can flag manifest changes, but it does not establish safety:

```bash
git diff main -- package.json
```

Use ecosystem-appropriate audit, provenance, secret, and policy tools in CI. Before accepting a package, establish that its benefit exceeds the long-term operational and security cost.
