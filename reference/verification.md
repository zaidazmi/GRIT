# Verification Reference

Load this reference when designing tests, evals, hardening, security gates, release evidence, or a completion verifier. [GRIT.md](../GRIT.md) defines the governing principles; the Quick Ship and Risky Ship playbooks define the required depth.

## Evidence before trust

Verification proves an observable claim. An agent’s confidence, explanation, or final message is not evidence.

Before editing an existing system, run the smallest relevant check and record the baseline. After editing, preserve the command, result, and artifact needed for another person or agent to reproduce the conclusion.

For risky work, separate implementation from final judgment. Protect acceptance evidence from being weakened by the implementer, add preservation cases, and use fresh adversarial evaluation where specification gaming would be costly.

## Verification ladder

Climb only as high as the risk requires, but do not skip a lower layer that applies.

```text
1. Format and schema
2. Build, type, and lint
3. Unit, integration, contract, and E2E behavior
4. Property, mutation, static, dependency, and security checks
5. Semantic or scenario evals for non-deterministic behavior
6. Proof in the running system: UI, API, logs, metrics, traces
7. Staged rollout and production observation
```

No single layer proves everything. A green unit suite cannot prove permissions, production configuration, usability, or the value of the feature.

## Test from the core outward

Priority order:

1. Core data structures and deterministic operations.
2. Scoring, matching, ranking, pricing, permissions, and other business rules.
3. API routes, schemas, and validation.
4. Async orchestration, queues, retries, idempotency, and failure states.
5. External integrations using fixtures, mocks, sandboxes, and occasional real tests.
6. User-facing critical paths with E2E or browser-driven proof.
7. Operational behavior through logs, metrics, traces, alerts, and recovery exercises.

At minimum for meaningful deterministic behavior, cover a happy path, an edge case, and an error or boundary case.

For bug fixes, reproduce the failure first. For existing systems, add preservation tests for behavior that must remain unchanged.

## Do not waste tests

Usually skip tests for:

- Pure styling and layout when visual proof is cheaper and clearer.
- Third-party library internals.
- Database driver behavior.
- SDK internals for auth, payments, or infrastructure.

Test your integration, configuration, assumptions, and failure handling—not the vendor’s implementation.

## Property and mutation testing

Example-based tests show that selected cases work. Properties express invariants that must hold across generated cases:

- Authorization never grants a broader role than the caller possesses.
- Retrying an idempotent operation does not duplicate its effect.

Property-based testing is especially useful for agent-written code because it searches beyond the cases the implementer anticipated. Mutation testing checks whether the suite fails when plausible defects are introduced. Use both selectively on high-value rules.

## LLM and agent evals

Avoid exact-prose assertions unless exact wording is a requirement. Start with schema, required fields, types, bounds, non-empty explanations, and valid tool arguments.

Shape does not prove meaning. Maintain a small, versioned eval set containing:

- Canonical successful examples.
- Counterexamples and refusal cases.
- Boundary, adversarial, and safety cases.
- Production incidents or support cases that are safe and lawful to replay.
- Expected tool-use and trace properties where the path matters.

Because behavior is non-deterministic:

- Run repeated trials where variance matters.
- Track first-attempt capability and repeated-run reliability separately.
- Record model, prompt, tool, dataset, and grader versions.
- Prefer deterministic graders when possible.
- Calibrate rubric-based model judges against human decisions.
- Inspect traces and environment failures when aggregate scores change.

Capability evals help discover what the system can do. Regression evals need explicit thresholds and should remain stable. Do not lower a threshold to absorb a newly discovered failure without an explicit decision and rationale.

## Hardening

For Risky Ship, measurable reliability, security, privacy, performance, accessibility, cost, observability, and recovery requirements belong in the Build Contract before implementation. Hardening verifies them and searches for omissions.

### Quick checks

- [ ] Rate limiting on new exposed endpoints.
- [ ] Timeout configuration for async and external operations.
- [ ] Input validation and sanitization at system boundaries.
- [ ] Resource cleanup for connections, files, and subscriptions.
- [ ] Audit logging for state-changing operations.

### Design checks

- [ ] Retry logic with backoff and idempotency.
- [ ] Graceful degradation when dependencies fail.
- [ ] PII handling across logs, caches, prompts, storage, and output.
- [ ] Concurrency safety for shared state.
- [ ] Latency, capacity, availability, accessibility, and cost budgets.
- [ ] Logs, metrics, traces, alerts, and recovery objectives.

## Security review

### Auth and access

- [ ] API routes check authentication.
- [ ] Users can access only authorized data.
- [ ] Authorization is enforced server-side.
- [ ] Webhooks verify signatures and replay protections where required.
- [ ] Production CORS and network exposure are intentional.

### AI and agent-specific

- [ ] Repository content outside designated trusted instructions, issues, web content, tool output, dependencies, and sub-agent responses are treated as untrusted input.
- [ ] External content cannot silently override trusted instructions or expand permissions.
- [ ] LLM outputs are validated before writes or actions.
- [ ] Coding and browser agents use appropriate filesystem and network isolation.
- [ ] Tools are allowlisted by capability.
- [ ] Credentials are scoped, short-lived, and unavailable unless required.
- [ ] Network egress and package installation follow explicit policy.
- [ ] High-impact actions are auditable and have a kill or revoke path.
- [ ] Logs redact secrets and sensitive personal data.

### Standard application security

- [ ] Public endpoints have rate limits and abuse controls.
- [ ] Environment variables and secrets are not exposed to clients.
- [ ] File uploads validate type, size, storage path, and access.
- [ ] Raw SQL uses parameterized queries.
- [ ] Error responses do not leak internal details.

### Automated gates

Use the project’s native static analysis, secret scanning, dependency audit, type checking, and equivalent gates.

Every project should declare its minimum required gate set. Every applicable change runs that set, and a failure blocks merge or release. A gate may be waived only by an authorized owner with the reason, risk, and compensating evidence recorded; changing tools must not weaken the protected claim.

Run applicable gates on every relevant change, not only before launch. Manual review remains necessary for design-level risks that scanners cannot establish.

## Release evidence

Before completion, preserve the Build Contract revision, task ID when used, baseline and commands, relevant test/eval/security results, runtime evidence, approved proof changes, residual risks and decisions, release identifier, rollout state, rollback or recovery path, and observation plan. When the observation window closes, append the result and decision.

“Tests pass” proves only the claims those tests cover. State conclusions at the same scope as the evidence.
