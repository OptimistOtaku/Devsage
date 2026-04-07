---
name: onboard-guide
description: "Generates a developer onboarding guide for this specific repository — covering mental model, architecture overview, key conventions, gotchas, and first-week goals — written from the codebase itself, not from a template."
allowed-tools: Bash Read Write
---

# Onboarding Guide Generator

You are writing the document that the most senior engineer on this team *would have written* for every new joiner — if they'd ever had the time. It gives a new developer a mental model of the system before they read a single source file. It is not a file inventory. It is not a README paraphrase. It is a curated introduction to how this system thinks.

## When this skill is invoked

- "Write an onboarding guide for this repo"
- "How would you explain this codebase to a new developer?"
- "Create a getting started doc"
- "What does someone new need to know?"

## How to execute this skill

### Step 1 — Read the whole repo structure

```bash
find . -type f | grep -v node_modules | grep -v .git | grep -v dist | grep -v ".next" | grep -v __pycache__ | sort
```

Then read:
- `README.md`
- `package.json` / `pyproject.toml` / `Cargo.toml` (understand scripts, dependencies)
- Any existing docs in `docs/` or `wiki/`
- Any `.env.example` (understand what external services are needed)

### Step 2 — Identify the architecture

Trace the request path from entry point to data layer:
- Where does the app start? (`index.ts`, `main.py`, `cmd/server/main.go`)
- What handles routing?
- Where is business logic?
- Where is the data layer?
- What external services are called, and where?

Identify the 5–8 most important directories and what each one owns.

### Step 3 — Extract the conventions that aren't documented

Read 3–5 files across different modules. Notice:
- How errors are handled
- How tests are structured
- How modules are organised
- Any patterns that repeat but aren't in the README

These are the things that trip up new developers most — they're never in docs.

### Step 4 — Find the gotchas

```bash
# Find TODO, HACK, FIXME, NOTE comments — they reveal known rough edges
grep -r "TODO\|FIXME\|HACK\|NOTE:" --include="*.ts" --include="*.py" --include="*.go" --include="*.js" . | grep -v node_modules | grep -v .git
```

Pick the most significant ones — things that would confuse a new developer without context.

### Step 5 — Write the guide

Save to `docs/ONBOARDING.md`. Structure:

```markdown
# Developer Onboarding Guide

> Written by DevSage on [date]. Based on the codebase at [short commit hash if available].

## What this system does

[Two paragraphs. What problem does this solve? Who uses it? Not marketing copy — the developer's understanding of the system's purpose.]

## How to think about this codebase

[The mental model. One key insight that makes the architecture make sense. Use an analogy if helpful. This is the paragraph a new developer will remember.]

## Architecture overview

[5–8 bullet points, each one a directory or layer, with one sentence on what it owns and why it exists separately.]

## The request path

[Trace one representative request from entry point to response. Be specific: file names, function names. This is the thread someone can pull to understand the whole system.]

## Conventions you won't find in the README

[3–6 patterns that repeat in the code but aren't documented. Name them, show an example file, explain why they exist.]

## Gotchas

[3–5 things that will confuse a new developer without context. Be blunt. "The auth middleware runs before the router, which means X." This section saves hours.]

## Getting started checklist

- [ ] [Specific environment setup step, with exact command]
- [ ] [Where to find credentials / secrets]
- [ ] [How to run tests]
- [ ] [How to run the dev server]
- [ ] [Which Slack channel / person to ask when stuck]

## Good first files to read

[5 files, in reading order, that give the best introduction to how this system works. One sentence on why each one.]
```

## Rules for this skill

- Every claim about the codebase must be grounded in files you've read — no invented patterns.
- The "How to think about this codebase" section is the most important paragraph in the document. Spend the most effort here.
- Do not pad with sections that have nothing useful to say. If there are no gotchas, omit the section.
- Write for a developer who is smart and experienced, but new to this specific system. Do not explain what a database is.
- After writing, append a note to `memory/runtime/key-decisions.md`: "Onboarding guide generated on [date] — covers [3 key topics]."
