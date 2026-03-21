# claude-todo-skill

**Ever lose track of that great idea you had mid-conversation?**

You're deep in a Claude session, something clicks — *"oh, I should add dark mode"* or *"need to write tests for that auth flow"* — and you think, I'll remember that. You won't. It's gone by the next session.

That's what this is for.

---

## What it does

`claude-todo-skill` gives you a `/todo` command inside Claude Code. Say it, type the idea, done. Claude drops it into a `TODOS.md` file right in your project. No context switching, no app to open, no friction.

When you're ready to do a brain dump review, `/todos review` walks you through each open item one by one — done, skip, or jot a quick update. Claude keeps the list tidy.

```
/todo write unit tests for the auth module
→ Added ✓

/todo look into dark mode toggle
→ Added ✓

/todos review
→ "write unit tests for the auth module" — Done, skip, or add an update?
```

---

## Commands

| Command | What happens |
|---|---|
| `/todo <text>` | Appends `- [ ] <text>` to your project's `TODOS.md` |
| `/todo` | Claude asks what you want to add, then adds it |
| `/todos` | Lists all your open (unchecked) items |
| `/todos review` | Walks through each open item interactively |

---

## Your TODOS.md

Lives in your project root — right next to your `DEVLOG.md` and `ROADMAP.md` if you use those. Looks like this:

```markdown
# TODOS
<!-- project: my-cool-project -->

- [ ] write unit tests for the auth module
- [ ] look into dark mode toggle
  - Update 2026-03-20: tried it, needs a theme context refactor first
- [x] add error handling to the API client
```

Flat list. No categories. No due dates. No overhead. Completed items stay right where they are, just checked off. That's intentional — this is a scratchpad, not a project management suite.

---

## Installation

Drop `SKILL.md` into your Claude Code skills directory:

```bash
mkdir -p ~/.claude/skills/todo
cp SKILL.md ~/.claude/skills/todo/SKILL.md
```

Restart Claude Code and `/todo` is ready to go in any project.

---

## Part of the claude-skills family

- **[claude-devlog-skill](https://github.com/code-katz/claude-devlog-skill)** — structured changelog for architectural decisions
- **[claude-roadmap-skill](https://github.com/code-katz/claude-roadmap-skill)** — living product roadmap with revision history
- **claude-todo-skill** ← you are here — lightweight scratchpad for everything else

---

## License

MIT
