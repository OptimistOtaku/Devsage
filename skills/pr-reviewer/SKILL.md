---
name: pr-reviewer
description: "Reviews a pull request or diff against the conventions already established in this codebase — not against an external style guide, but against what this team actually does."
allowed-tools: Bash Read Write
---

# PR Reviewer

You are reviewing a change against *this specific codebase*. Your job is not to apply general best practices from the internet. Your job is to compare the incoming change against the patterns, conventions, and standards that already exist here — and flag deviations, risks, and improvements with precise references.

## When this skill is invoked

- "Review this PR" / "review this diff"
- "Does this change fit our conventions?"
- "What's wrong with this code?"
- User pastes a diff or a set of changed files

## How to execute this skill

### Step 1 — Extract the conventions of this codebase

Before touching the diff, understand what "good" looks like here. Sample 3–5 existing files in the same module or directory as the changed files:

```bash
# Find files adjacent to the changed files
ls src/[relevant-directory]/

# Read a representative sample
cat src/[relevant-directory]/[file1].ts
cat src/[relevant-directory]/[file2].ts
```

Look for:
- Naming conventions (camelCase, kebab-case, PascalCase, where each applies)
- Error handling patterns (try/catch vs. Result types vs. thrown errors)
- Import ordering and grouping
- Function length and responsibility scope
- Test file conventions (co-located vs. `__tests__/`, naming patterns)
- Comment style (JSDoc, inline, none)

### Step 2 — Check for project-level style config

```bash
cat .eslintrc* 2>/dev/null || cat .eslintrc.js 2>/dev/null
cat .prettierrc* 2>/dev/null
cat tsconfig.json 2>/dev/null
```

If a linter config exists, your review supplements it — focus on things linters miss (logic, architecture, convention).

### Step 3 — Analyse the diff

Review the change against what you found. Organise findings into four categories:

**🔴 Must fix** — correctness issues, security risks, broken error handling, logic bugs, anything that could cause a production incident.

**🟡 Should fix** — convention deviations that will create inconsistency or confusion, missing tests for critical paths, unclear naming that will cause maintenance pain.

**🟢 Consider** — optional improvements, refactors that would be nice but aren't blocking, patterns the team might want to adopt.

**✅ Looks good** — call out what's done well. A review that only lists problems is demoralising and incomplete.

### Step 4 — Format the review

```
## PR Review

**Summary:** One sentence on what this change does and whether it's broadly on the right track.

---

### 🔴 Must fix

**[File:line]** — Issue description.
→ Convention: [what this codebase does instead, with a reference to an example file]
→ Risk: [what goes wrong if this isn't fixed]

[repeat for each issue]

---

### 🟡 Should fix

**[File:line]** — Issue description.
→ Convention: [reference]

---

### 🟢 Consider

**[File:line]** — Suggestion.

---

### ✅ Looks good

- [specific things done well, with file references]

---

**Verdict:** APPROVE / REQUEST CHANGES / NEEDS DISCUSSION
```

## Rules for this skill

- Every 🔴 and 🟡 finding must reference a specific file and line in the diff.
- Every convention claim must point to an example in the existing codebase — do not say "the convention is X" without showing where that convention already exists.
- Do not raise issues that the existing codebase also has everywhere — if inconsistency is universal, note it as "existing pattern" not a deviation.
- If the diff is not provided and you cannot access the branch, ask the user to paste it.
- Security findings always go in 🔴, regardless of whether they match conventions.
