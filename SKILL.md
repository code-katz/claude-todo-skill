---
name: todo
description: Maintain a per-project TODOS.md checklist for tracking ideas and loose tasks. Use this skill whenever the user says "/todo", "/todos", "todo", "todos", "add a todo", "show my todos", or "review todos". Works across any project — not tied to a specific repo or product.
---

# Todo Skill

This skill maintains a `TODOS.md` checklist in the root of a project's directory. It's a simple scratchpad for ideas and loose tasks — not a sprint board, not a roadmap. Just checkboxes.

This skill is **project-agnostic** — it works with any repository. On first use in a session, it identifies the active project and adapts accordingly.

## Why This Matters

Ideas and small tasks surface mid-conversation and disappear just as fast. This skill gives them a home — a flat, editable list you can add to in seconds and review when you're ready. No metadata, no categories, no overhead.

## Project Context (First Use Per Session)

The first time this skill triggers in a session, establish the project context by checking for clues in this order:

1. **Memory/conversation context** — Does Claude already know what project the user is working on? If so, confirm: "I'll add this todo to [Project Name] — correct?"
2. **CWD / git remote** — Check the working directory name or `git remote -v` to infer the project name.
3. **Ask the user** — If neither source provides clarity, ask: "Which project should I add this to?"

TODOS.md lives in the **project root** — the same place as DEVLOG.md and ROADMAP.md.

### Lint Check

When establishing project context for the first time in a session, verify that the project has a linter configured. Check for stack-appropriate lint configuration files:

- **Python**: `ruff.toml`, `pyproject.toml` with `[tool.ruff]`, `.flake8`
- **JavaScript/TypeScript**: `.eslintrc*`, `eslint.config.*`, `biome.json`, or a `lint` script in `package.json`
- **Swift/iOS**: `.swiftlint.yml`
- **Go**: `.golangci.yml`
- **Rust**: `clippy` configuration in `Cargo.toml`
- **General**: `.pre-commit-config.yaml`

If no linter is configured, flag it to the user before proceeding. Recommend: Ruff for Python, ESLint or Biome for JS/TS, SwiftLint for Swift, golangci-lint for Go, clippy for Rust. Frame it as a prerequisite, not an afterthought.

---

## TODOS.md Format

```markdown
# TODOS
<!-- project: <project-name> -->

## <Section Name>

### Completed
- [x] completed thing

### Ready — Can Run in Parallel
- [ ] task description → **Persona** | context or output note
- [ ] another task → **Persona A + Persona B** | dependency note
  - Update 2026-03-20: partial progress note here

### Blocked Chain (sequential — each depends on the previous)
1. [ ] first thing → **Persona** | blocker description
2. [ ] next thing → **Persona** | blocked: needs #1
3. [ ] third thing → **Persona** | blocked: needs #2

### Deferred
- [ ] future task → **Persona** | reason for deferral
```

**Rules:**
- Organize by domain sections (e.g., "Product & Engineering", "Business Operations")
- Within each section, group items by execution state:
  - **Completed** — done items (`- [x]`)
  - **Ready — Can Run in Parallel** — unblocked items that can execute concurrently
  - **Blocked Chain** — sequential items where each depends on the previous (use numbered list `1. [ ]`)
  - **Deferred** — items intentionally postponed with a reason
- Every open item must include a **persona tag** after `→`: the team member best suited to lead it (e.g., `→ **Akira**`, `→ **River + Akira**`, `→ **Sage** + attorney`)
- After the persona tag, add `|` followed by a brief context note: dependency, output, or blocker description
- Progress notes: indented sub-bullets with a date prefix: `  - Update YYYY-MM-DD: note`
- When adding a new item, ask: "Who should lead this, and can it run in parallel with current work?" if the user doesn't specify
- File is created on first use with the header above

---

## Trigger Conditions

This skill activates when the user says:

- `/todo <text>` — add a new todo item
- `/todo` (no args) — prompt for text, then add
- `/todos` — list all open (unchecked) items
- `/todos review` — interactive review of all open items

---

## Sub-Commands

### `/todo <text>` — Add an item

1. Read TODOS.md (create it if it doesn't exist)
2. Determine the right section and group for the item:
   - If the user specifies a persona, use it. If not, ask: "Who should lead this?" (suggest based on the item's domain)
   - If the user specifies parallel/blocked status, use it. If not, infer: items with no dependencies go to "Ready — Can Run in Parallel"
3. Append the item in the correct group with persona tag and context note
4. Write the file
5. Confirm: "Added: `<text>` → **Persona** (Ready — parallel)" or similar

If called with no args (`/todo`), ask: "What's the todo?" then proceed with their answer.

**Creating TODOS.md for the first time:**

```markdown
# TODOS
<!-- project: <project-name> -->

## General

### Ready — Can Run in Parallel
- [ ] <first item> → **Persona** | context
```

Replace `<project-name>` with the project name established during session setup.

---

### `/todos` — List open items

1. Read TODOS.md (if it doesn't exist, say "No todos yet — use `/todo <text>` to add one")
2. Collect all lines matching `- [ ]`
3. Display them as a numbered list:

```
Open todos for <project-name>:

1. idea or task description
2. another idea
3. third item
```

If no open items: "All clear — no open todos."

---

### `/todos review` — Interactive review

Walk through each open `- [ ]` item one at a time. For each:

1. Display the item text
2. Ask: **"Done, skip, or add an update?"**
3. Handle the response:
   - **Done** (or "d", "yes", "✓") → change `- [ ]` to `- [x]`
   - **Skip** (or "s", "next", "n") → leave unchanged, move to next item
   - **Update** (or "u", or any other text) → if they type "update" or "u", ask for the note text; if they type something else directly, treat it as the note — append `  - Update YYYY-MM-DD: <note>` as a sub-bullet under the item
4. After all items, write the updated TODOS.md
5. Show a summary: "Review complete. X marked done, Y updated, Z skipped."

**If no open items:** "No open todos — you're clear!"

**Date format for updates:** Use today's date from the system context (format: YYYY-MM-DD).

**After the review loop:** Write the file once with all changes applied. Do not write on each individual item change.

---

## Workflow: Writing to TODOS.md

### Always read before writing
Read the current TODOS.md before any write operation so you don't overwrite existing content.

### No approval gate for `/todo`
Adding a single item is low-stakes — write it immediately and confirm. Do not ask "is this okay?" before writing.

### Show changes before writing for `/todos review`
After the review loop, show the user what changed (e.g., "Marking 2 done, adding 1 update note") before writing the file. Then write it.

### No git commit
This skill does **not** commit or push TODOS.md. The file is intentionally low-ceremony — it's a scratchpad, not a changelog. The user can commit it manually if they want it in source control.

---

## Reading TODOS.md

When the user asks "what are my todos?" or "show my todos" outside of a formal `/todos` call, read TODOS.md and list open items inline in the conversation. No special formatting required.

---

## Style Guidelines

- Keep item text short and imperative: "write unit tests for auth" not "I need to remember to maybe write some unit tests for the authentication module"
- Always include a persona tag (`→ **Persona**`) on open items — this is required, not optional
- Always include a context note after `|` — dependency, output description, or blocker
- When marking an item done, move it to the "Completed" group in its section
- When an item becomes unblocked (its dependency is resolved), move it from "Blocked Chain" to "Ready — Can Run in Parallel"
- Do not reorder items within a group — preserve the order the user added them
- Never delete items — mark done with `[x]` or leave with `[ ]`; only the user can ask you to remove something explicitly
- When the user adds an item without specifying a persona, suggest one based on the item's domain (engineering → Akira, product → River, marketing → Toni, business/legal/finance → Sage, testing → Robin, frontend → Sasha, devops → Alex, security → Morgan, data → Jordan/Casey, project management → Quinn)
