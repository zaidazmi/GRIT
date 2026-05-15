# GRIT

**Guidelines and Rules for Iterating on Things (with AI)**

I'm a product person, not a software engineer. I've been building startups for over a decade, working closely with developers on dozens of projects but never writing the code myself. All those years sitting next to engineers, reviewing PRs, arguing about architecture, and debugging production issues made me technical enough that when AI coding tools showed up, I could actually use them. AI didn't teach me how software works. It just finally let me build it. For the last two years I've been shipping real code, and watching it quietly break in ways I didn't think were possible. So I wrote down the stuff that actually helped. Not what sounds good in a blog post. The stuff that stopped me from approving pull requests that looked fine on Monday and fell apart on Tuesday. I also spent a lot of time looking at how other people handle this: test-driven approaches, spec-driven workflows, review-heavy processes. I took what worked and left the rest.

GRIT is a quality-control workflow for anyone using AI agents (Claude Code, Cursor, Codex, Windsurf, others) to write real software. It's the accumulated scar tissue of building with AI daily, reading how others do it, and noticing which habits actually hold up.

The core idea is a loop: **Spec -> Test -> Implement -> Review -> Ship.** Process isn't fun, but AI is confident and wrong often enough that you need a system to catch it.

## What's inside

[GRIT.md](GRIT.md) is the full guide. It covers:

- **The Loop** - a six-step cycle that makes AI-generated code inspectable before it ships
- **Context Hygiene** - how to feed AI agents without poisoning them with stale conversation history
- **AI/LLM Coding Patterns** - structured outputs, prompt injection defense, model selection
- **Architecture Principles** - keeping AI-generated code testable and untangled
- **Testing Strategy** - what to test, what to skip, how to verify LLM outputs
- **Version Control Workflow** - git worktrees, branching, commit hygiene for AI agents
- **Security Checklist** - the stuff that will wake you up at 3am if you skip it
- **Shipping Rules** - ten rules, none of which involve stone tablets

## Who this is for

You use AI to write code that matters (features, integrations, data models, business logic) and you've noticed that "the AI wrote it and it looks fine" is not a shipping criterion. You want to go fast but you also want to sleep at night.

## Who this is not for

You're looking for a prompt library, a model leaderboard, or permission to skip code review because the AI is "really good now." GRIT can't help you there.

## Quick start

1. Read [GRIT.md](GRIT.md).
2. Try the loop on your next non-trivial feature.
3. Adjust the ceremony to match the risk. A config change doesn't need a spec. A payments integration does.

That's it. One markdown file and the discipline to use it.
