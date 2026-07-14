# GRIT

**Guidelines and Rules for Iterating on Things (with AI)**

Lean contracts. Bounded loops. Fresh contexts. Independent evidence. Verification before trust.

---

AI agents let product managers, founders, indie hackers, and small startup teams build software at a speed that used to require a larger engineering team. They also suppress errors silently, invent APIs that do not exist, and produce implementations that look finished but fail with real users. The models are getting better. The need for judgment and proof is not going away.

GRIT is a lightweight, risk-adaptive way to ship AI-assisted and agent-built software that holds up under real traffic and real users. It is designed first for product people and fast-moving teams—not only established engineering organizations. You do not need to become a senior engineer to use it. You need to own the problem, make the important decisions, and require evidence before shipping.

Most work should use a ten-minute Quick Ship process. GRIT adds deeper controls only for authentication or permissions, payments, sensitive user data, database migrations, destructive actions, public APIs, or changes that are difficult to undo.

**One delivery loop. Bounded execution inside it. No shipping on faith.**

---

## Why GRIT Exists

I'm a product person, not a software engineer. Over a decade of building startups, sitting next to developers, reviewing PRs, arguing about architecture, debugging production fires. When AI coding tools arrived, that background was enough to start shipping real code. For the last two years I've been building with AI daily — and watching it quietly break in ways I didn't think were possible.

GRIT is accumulated scar tissue. The stuff that actually stopped bad code from shipping. Not what sounds good in a blog post. The habits that held up after hundreds of features and too many 3am debugging sessions caused by code that "looked fine."

I also spent time studying how others handle this: spec-driven workflows, test-first approaches, multi-agent orchestration, governance frameworks, and the research on AI code failure modes. I took what worked and left the rest.

---

## The Loop

```text
SPEC -> TEST -> IMPLEMENT -> REVIEW -> HARDEN -> SHIP
  ^                                                 |
  |_____________ route findings back _______________|
```

Every non-trivial feature follows this cycle, but the weight changes with the risk. A tiny UI change may only need a compact contract and visual proof. A payments or auth change needs the full loop, rollback thinking, and adversarial review. Long-running agents operate inside a smaller bounded loop: orient, act, observe, verify, then finish, retry, or escalate within explicit time/cost limits. Intent and expectations are your leverage. Independent evidence is your proof.

## Default: Quick Ship

Start every shipping task with [Quick Ship](playbooks/quick-ship.md). Open [Risky Ship](playbooks/risky-ship.md) only when the change has a visible trigger:

- Authentication or permissions.
- Payments.
- Sensitive user data.
- Database migrations.
- Destructive actions.
- Public APIs or contracts other systems depend on.
- Changes that are difficult to undo.

If none applies, stay in Quick Ship. If investigation reveals one, switch modes before implementation.

---

## What's Inside

GRIT uses progressive disclosure: one canonical framework, task-specific playbooks, and deeper references.

| File | Use it for |
| --- | --- |
| [**Founder/PM Quickstart**](playbooks/founder-pm-quickstart.md) | Ship a feature safely without reading the whole framework |
| [**GRIT.md**](GRIT.md) | Understand the framework, choose a mode, and route failures |
| [**Quick Ship — default**](playbooks/quick-ship.md) | Normal product and engineering changes |
| [**Risky Ship — triggered**](playbooks/risky-ship.md) | Auth or permissions, payments, sensitive user data, database migrations, destructive actions, public APIs, or difficult rollback |
| [**Autonomous Loop**](playbooks/autonomous-loop.md) | Long-running, scheduled, recurring, or unattended agents |
| [**Verification**](reference/verification.md) | Tests, evals, hardening, security gates, and release evidence |
| [**Agent Operations**](reference/agent-operations.md) | Context, permissions, architecture, Git, multi-agent work, and dependencies |

New users should start with the Founder/PM Quickstart. AI agents should start with project instructions, the task contract, and one applicable playbook; load a reference only when the task requires it.

---

## Who This Is For

- Product managers turning product intent into working software with AI agents.
- Indie hackers and solo founders who need speed without avoidable production fires.
- YC-style startups and small teams shipping before they have specialized infrastructure or QA roles.
- Engineers and product leads who need a shared, lightweight definition of "ready to ship."
- Established teams that want deeper controls for high-risk agent work.

GRIT is IDE-, agent-, model-, and provider-agnostic. It works in any development environment that can inspect the project, edit code, run the project’s checks, and preserve evidence. Claude Code, Codex, Cursor, Windsurf, Copilot, Gemini CLI, and future tools are examples—not dependencies or preferred implementations.

---

## Quick Start

1. Open the [Founder/PM Quickstart](playbooks/founder-pm-quickstart.md).
2. Describe the problem, smallest useful change, boundaries, and visible proof.
3. Use [Quick Ship](playbooks/quick-ship.md) unless a risk trigger sends you to [Risky Ship](playbooks/risky-ship.md).
4. Let the agent investigate and propose the technical approach; you retain product intent and final judgment.
5. Build the smallest slice, verify it independently, review in a fresh context, and watch it after release.

Use the minimum process that prevents avoidable rework. Every artifact, meeting, agent pass, and check must clarify intent, reduce a visible risk, or produce reproducible evidence. If it does none of those things, skip it. Agent speed matters only when it reduces time to an accepted outcome—not when it merely generates more code faster.
