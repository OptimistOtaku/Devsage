# DevSage 🧙

> *Your codebase's institutional memory — a senior engineer that lives in the repo.*

DevSage is a GitHub Copilot agent that answers the question every engineering team eventually faces: **what happens when the people who built this leave?** It reads your codebase, understands your conventions, and gives every developer — new or veteran — a trusted source of architectural knowledge.

It doesn't write code. It doesn't run deployments. It doesn't make autonomous decisions. It reads, reasons, and explains — and does those three things better than any generic AI assistant because it grounds every answer in *your actual repository*.

---

## What DevSage Does

| Capability | What it means in practice |
|---|---|
| **Architecture Explainer** | Ask *why* the auth module is structured the way it is. Get an answer backed by specific file paths and honest tradeoff analysis — not a generic lecture. |
| **PR Reviewer** | Reviews a diff against *your team's conventions*, not a style guide from the internet. Every finding references a real file. |
| **Onboarding Guide** | Generates a developer onboarding doc written from the codebase itself — mental models, gotchas, conventions that never make it into the README. |
| **ADR Writer** | Drafts Architecture Decision Records, both prospective (planning a decision) and retrospective (documenting one that's already in the code). |

---

## Quick Start

DevSage is packaged as a Copilot agent. To use it in your repository:

1. **Reference the agent** in your workflow or invoke it via the Copilot agent interface.
2. **Ask a question** — DevSage will orient itself to your codebase before answering anything.
3. **Get grounded answers** — every claim will reference a specific file, not a general pattern.

No setup. No config. No environment variables. DevSage reads what's already there.

---

## Skills

### 🏗️ `arch-explainer`
Explains *why* your codebase is built the way it is. Not just what the code does — anyone can read the code — but the reasoning, tradeoffs, and intent behind the structure.

**Invoke it when:**
- "Why is the auth module structured this way?"
- "Explain the overall architecture."
- "Walk me through how [feature] works end to end."

### 🔍 `pr-reviewer`
Reviews a pull request against the conventions *already established in this codebase* — not against an external style guide. Organises findings into must-fix, should-fix, consider, and looks-good categories.

**Invoke it when:**
- "Review this PR."
- "Does this change fit our conventions?"
- Pasting a diff for review.

### 📖 `onboard-guide`
Generates the onboarding document your most senior engineer would have written for every new joiner — if they'd ever had the time. Saves to `docs/ONBOARDING.md`.

**Invoke it when:**
- "Write an onboarding guide for this repo."
- "How would you explain this codebase to a new developer?"
- "What does someone new need to know?"

### 📋 `adr-writer`
Drafts Architecture Decision Records with full context: the decision, alternatives considered, rationale, and consequences. Works both prospectively and retrospectively from existing code.

**Invoke it when:**
- "Write an ADR for [decision]."
- "Document why we chose X over Y."
- "What decisions in this codebase should be documented?"

---

## Workflows

### `pr-review-flow`
A full PR intelligence pipeline that chains all four skills together:

```
Orient → Review → Architectural Impact → ADR (if needed) → Summary
```

1. **Orient** — maps which modules the change touches and whether it crosses architectural boundaries
2. **Review** — full code review against existing conventions
3. **Arch Impact** — explains how the change affects the system's design direction
4. **ADR** — auto-generated if the change encodes a significant new decision
5. **Summary** — a concise verdict with the three most important actions for the author

**Triggers:** `pull_request` events or manual invocation.

---

## Design Principles

**Read-only by design.** DevSage never edits source files, never runs builds, never commits or pushes. It can only write to `memory/`, `docs/`, and `ADR/`. This is not a limitation — it's what makes it safe to give it full read access to a production codebase.

**Source over memory.** Every architectural claim is backed by a specific file and line number. DevSage never answers from general knowledge alone. If it can't find evidence in the repo, it says so.

**Convention over preference.** When reviewing code, DevSage compares against what *this team* actually does — not what it would personally prefer, and not what some external guide recommends.

**Honesty about debt.** If code is messy, provisional, or inconsistent with itself, DevSage says so plainly. Developers can tell when they're being managed.

---

## Memory

DevSage maintains a persistent session memory in `memory/runtime/`:

- `context.md` — session log with repo, stack, and starting request
- `key-decisions.md` — architectural discoveries accumulated across sessions

This means DevSage gets smarter about *your specific repository* the more you use it. Insights from a Monday architecture session are available on Friday's PR review.

---

## Compliance

DevSage operates at **low risk tier** with built-in segregation of duties:

| Role | Permissions | Skills |
|---|---|---|
| `analyst` | read, analyse, explain | `arch-explainer` |
| `reviewer` | read, review, comment | `pr-reviewer` |
| `author` | read, write, create | `onboard-guide`, `adr-writer` |

DevSage must not be both the **author** and the **approver** of the same decision. If DevSage writes an ADR, a human engineer reviews and approves it before merge.

Audit logging is enabled. Retention: 90 days.

---

## Repository Structure

```
devsage/
├── agent.yaml              # Agent spec — model, skills, compliance config
├── skills/
│   ├── arch-explainer/     # Architecture explanation skill
│   ├── pr-reviewer/        # PR review skill
│   ├── onboard-guide/      # Onboarding guide generator
│   └── adr-writer/         # ADR drafting skill
├── workflows/
│   └── pr-review-flow.yaml # Full PR intelligence pipeline
├── hooks/
│   └── bootstrap.md        # Session orientation routine
├── memory/
│   └── runtime/            # Persistent session context
├── SOUL.md                 # Core identity and values
├── DUTIES.md               # Permitted and prohibited actions
└── RULES.md                # Operational rules
```

---

## Model

DevSage prefers `claude-sonnet-4-5` with fallbacks to `gpt-4o` and `gemini-2.0-flash`. Model selection is transparent and configurable in `agent.yaml`.

---

## What DevSage Is Not

- **Not a code generator.** It won't write your features.
- **Not an autonomous agent.** Every recommended action requires a human to execute it.
- **Not a generic AI assistant.** It doesn't answer from general knowledge — only from what's actually in your repository.

It is the senior engineer who has been here since the beginning, who knows where all the bodies are buried, and who actually has time to explain things.

---

*Built with the [GitHub Copilot Extensibility Platform](https://github.com/features/copilot).*
