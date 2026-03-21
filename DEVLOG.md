# claude-todo-skill — Development Log

A living record of architectural decisions, milestones, key insights, and strategic direction.
Auto-maintained via [claude-devlog-skill](https://github.com/d6veteran/claude-devlog-skill). Entries are reverse-chronological.

---

## [2026-03-20] v1.0 — Initial release

**Category:** `milestone`
**Tags:** `release`, `v1`, `initial`
**Risk Level:** `low`
**Breaking Change:** `no`

### Summary
First release of the claude-todo-skill. A lightweight, per-project todo tracker that lives alongside devlog and roadmap skills.

### Detail
Built to solve a simple problem: ideas and small tasks surface mid-conversation and disappear. This skill gives them a home — a flat `TODOS.md` checklist with three interactions: add, list, and review.

Design decisions:
- No categories, priorities, due dates, or tags — kept deliberately minimal per user intent ("just a list of ideas to track")
- No git commit on write — TODOS.md is a scratchpad, not a changelog; user can commit manually if desired
- No approval gate for `/todo` add — single-item writes are low-stakes, confirm immediately
- Show-before-write for `/todos review` — multi-item changes shown first, then written once
- Completed items stay inline as `- [x]` — never moved to a separate section
- Progress notes use indented sub-bullets with date prefix

### Decisions Made
- **Flat list vs. sections:** Chose flat. Adding sections (Open / Done) adds ceremony for no benefit at this scope.
- **Commit on write:** Chose not to. Devlog and roadmap commit because they're architectural artifacts. Todos are transient scratchpad content.
- **One skill file vs. two:** Chose one. Both `todo` and `todos` commands are handled in a single SKILL.md, triggered by matching invocation.
- **Delete option in review:** Not included in v1. "Done" is sufficient for clearing items; if the user wants to remove an item, they can ask explicitly.
