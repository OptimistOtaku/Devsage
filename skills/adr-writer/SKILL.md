---
name: adr-writer
description: "Drafts an Architecture Decision Record (ADR) for a given decision — either from a user description, or by inferring an undocumented decision that already exists in the codebase."
allowed-tools: Bash Read Write
---

# ADR Writer

You write Architecture Decision Records — the documents that capture not just *what* was decided, but *why*, *what alternatives were considered*, and *what consequences follow*. These are the documents that save future engineers from re-litigating decisions that were already made thoughtfully.

You can write ADRs in two modes:

1. **Prospective** — the user is about to make a decision and wants to document it
2. **Retrospective** — a decision was already made and is embedded in the code; you infer the ADR from the evidence

## When this skill is invoked

- "Write an ADR for [decision]"
- "Document why we chose X over Y"
- "We decided to use Redis for sessions — write that up"
- "What decisions in this codebase should be documented?"

## How to execute this skill

### For prospective ADRs

The user will describe the decision, context, and options considered. If they haven't provided all of these, ask once for what's missing before drafting. Do not ask for information that can be inferred from the codebase.

### For retrospective ADRs

When the user asks you to document an existing decision, trace it in the code:

```bash
# Find the implementation
grep -r "keyword" --include="*.ts" --include="*.py" . | grep -v node_modules

# Check git history for context
git log --oneline --all -- path/to/relevant/file | head -20
git show [commit-hash] --stat
```

Read the implementation, check if there are any comments explaining the choice, and check git messages for context. Build the ADR from what you find — fill gaps with "inferred from implementation" rather than invention.

### Check the ADR sequence

```bash
ls docs/decisions/ 2>/dev/null || ls ADR/ 2>/dev/null || ls docs/adr/ 2>/dev/null
```

Find the highest-numbered existing ADR and increment by one.

If no ADR directory exists, create `docs/decisions/` and note in your response that the user should also add it to `.gitignore` exclusions if needed.

### Write the ADR

Save to `docs/decisions/ADR-[NNN]-[short-title].md`:

```markdown
# ADR-[NNN]: [Short title describing the decision]

**Date:** [YYYY-MM-DD]
**Status:** [Proposed | Accepted | Deprecated | Superseded by ADR-XXX]
**Deciders:** [Who made this decision — leave blank if unknown]

---

## Context

[What is the situation that requires a decision? What forces are at play — technical constraints, team conventions, performance requirements, operational concerns? Keep this factual, not argumentative. 2–4 sentences.]

## Decision

[State the decision clearly and directly. "We will use X for Y." Not "it was decided that we might consider..." One paragraph maximum.]

## Alternatives Considered

### [Option A — the chosen option]
**Pros:** [bullet points]
**Cons:** [bullet points]

### [Option B]
**Pros:** [bullet points]
**Cons:** [bullet points]

### [Option C, if applicable]
**Pros:** [bullet points]
**Cons:** [bullet points]

## Rationale

[Why was the chosen option selected over the alternatives? What was the deciding factor? If the decision involved tradeoffs, name them honestly — "we accepted X in exchange for Y."]

## Consequences

**Positive:**
- [What becomes easier or better as a result of this decision]

**Negative / Tradeoffs:**
- [What becomes harder, what debt is accepted, what doors close]

**Neutral:**
- [Downstream effects that are neither good nor bad, but important to know]

## Implementation Notes

[Optional. Specific files, patterns, or constraints that follow from this decision. Link to the relevant code.]
```

### After writing

Tell the user the exact commit command:
```
git add docs/decisions/ADR-[NNN]-[short-title].md && git commit -m "docs: ADR-[NNN] [short title]"
```

Append to `memory/runtime/key-decisions.md`:
```
- ADR-[NNN]: [Title] — [one-line summary of the decision]
```

## Rules for this skill

- The **Decision** section must be a clear, unambiguous statement. If it cannot be stated in one paragraph, the decision is not yet clear enough to document.
- The **Rationale** section must name the deciding factor. "It was the best option" is not a rationale.
- For retrospective ADRs, mark any inferred content with "(inferred from implementation)" so future readers know what was documented vs. deduced.
- Status should be `Accepted` for retrospective ADRs unless the user says otherwise. Use `Proposed` for prospective ADRs that haven't been implemented yet.
- Never write an ADR for a decision that is trivial or purely stylistic (use camelCase vs. snake_case). ADRs are for decisions with non-obvious tradeoffs.
