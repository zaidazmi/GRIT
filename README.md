# GRIT

**Guidelines and Rules for Iterating on Things (with AI)**

Lean specs. Failing tests. Fresh contexts. Adversarial review. Verification before trust.

---

AI agents write code fast. They also suppress errors silently, hallucinate APIs that don't exist, and produce implementations that pass every test on Monday and fall apart in production on Tuesday. The models are getting better. The failure modes are not going away.

GRIT is the quality-control system that makes agentic coding actually work. Not "write better prompts" advice. Not a prompt library. A loop for shipping AI-generated code that holds up under real traffic and real users.

**One loop. Ten sections. No shipping on faith.**

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

Every non-trivial feature follows this cycle, but the weight changes with the risk. A tiny UI change may only need a compact spec and visual proof. A payments or auth change needs the full loop, rollback thinking, and adversarial review. The spec is your leverage. The tests or manual checks are your proof. Findings route back to the phase that caused them, not patched over downstream.

---

## What's Inside

[**GRIT.md**](GRIT.md) is the full methodology. 10 sections:

| # | Section | What it covers |
|---|---------|----------------|
| 1 | **The Loop** | Mode selection, challenge premise, spec, test, implement, review, harden, ship |
| 2 | **Context Hygiene** | How to feed agents without poisoning them with stale history |
| 3 | **Agent Constraints** | Non-negotiable rules, programmatic enforcement, what agents must never do |
| 4 | **Architecture Principles** | Pure core / effectful shell, premature abstraction traps |
| 5 | **Testing Strategy** | What to test, what to skip, how to verify LLM outputs by shape |
| 6 | **Version Control** | Git worktrees, branching, commit hygiene for parallel agents |
| 7 | **Security Checklist** | Manual checks + automated gates (SAST, secret scanning, dependency audit) |
| 8 | **Multi-Agent Orchestration** | Coordination rules, task decomposition, migration safety |
| 9 | **Dependency Governance** | Rules for when agents want to `npm install` the entire registry |
| 10 | **Feedback Loops** | Every recurring mistake becomes a rule. The process improves itself. |

---

## Who This Is For

- Engineers shipping real products with AI agents — not toy demos.
- Solo builders who want speed without recklessness.
- Teams that noticed "the AI wrote it and it looks fine" is not a shipping criterion.
- Leads reviewing AI-generated PRs who need a repeatable standard.
- Anyone who wants to sleep at night after deploying AI-written code.

Works with **Claude Code, Cursor, Codex, Windsurf, Copilot, Gemini CLI** — any agentic coding environment that can read files, edit code, and run tests.

---

## Quick Start

1. Read [GRIT.md](GRIT.md).
2. Pick a mode: Spike, Quick Ship, or Risky Ship.
3. Write the smallest spec that preserves intent. For existing systems, describe only what changes.
4. Build, verify with tests or a manual proof, run a fresh review, then ship with a rollback path.
5. When a mistake recurs, add it to your rules file. The system gets smarter.

That's it. One markdown file and the discipline to use it.
