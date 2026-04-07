# Bootstrap

Run this routine at the start of every new session before responding to any user message.

## Step 1 — Orient to the repository

Run the following commands in order to build a structural map of the codebase:

```bash
# Top-level structure
find . -maxdepth 2 -type f | grep -v node_modules | grep -v .git | grep -v dist | grep -v .next | sort

# Package manifest (understand dependencies and scripts)
cat package.json 2>/dev/null || cat pyproject.toml 2>/dev/null || cat Cargo.toml 2>/dev/null || cat go.mod 2>/dev/null

# README
cat README.md 2>/dev/null || cat readme.md 2>/dev/null
```

## Step 2 — Load persistent memory

Check for prior session context:

```bash
cat memory/runtime/context.md 2>/dev/null
cat memory/runtime/key-decisions.md 2>/dev/null
```

If these files exist, load them silently. They represent knowledge from prior sessions — treat them as your own prior work, not as external documents.

## Step 3 — Check for existing architectural documentation

```bash
ls docs/ 2>/dev/null
ls ADR/ 2>/dev/null || ls docs/decisions/ 2>/dev/null || ls docs/adr/ 2>/dev/null
```

If ADRs or docs exist, note their topics. Do not read them all — just catalogue what exists so you can reference them when relevant.

## Step 4 — Note the technology stack

From `package.json`, imports in `src/`, or directory conventions, identify:
- Primary language and runtime
- Main frameworks (e.g. Next.js, Express, FastAPI)
- Key infrastructure dependencies (databases, queues, auth)

Store this in working memory for the session.

## Step 5 — Update session log

Append to `memory/runtime/context.md`:

```
## Session — [today's date]
- Repo: [name from package.json or directory]
- Stack: [what you found]
- Prior context: [loaded / none]
- Starting request: [first user message, one line]
```

## On completion

Confirm orientation silently — do not narrate the bootstrap process to the user. Just be ready. The first response should be the answer to their question, not a report on what files you read.
