# TaskMark

[![CI](https://github.com/taskmark/taskmark-spec/actions/workflows/ci.yml/badge.svg)](https://github.com/taskmark/taskmark-spec/actions/workflows/ci.yml)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

**Plain-text todos that work everywhere.**

Write tasks in Markdown. Track them in git. Never lose your todos again.

**Version:** 2.0.1

---

## Why TaskMark?

Your todos should be as simple as your code:

- **Plain text** — Works in any editor, forever
- **Git-friendly** — Version control your tasks like your code
- **Markdown-based** — Looks good everywhere (GitHub, VSCode, terminal)
- **Organized** — Sections with metadata inheritance
- **Powerful** — Recurring tasks, subtasks, priorities, estimates, notes

---

## Quick Start

### The Basics

```markdown
# My Project

- [ ] Review pull request @alice
- [x] Update dependencies @bob done:2024-03-10
- [ ] Write tests @charlie due:2024-03-15
```

That's it. You're using TaskMark. Everything else is optional.

### Add Some Structure

```markdown
# Acme Project +Acme #work

## Backend +Backend #critical

- [ ] (A) Fix database connection @alice planned:2024-03-10 due:2024-03-15 ~8h
  - [ ] Update connection pooling ~2h
  - [ ] Add retry logic ~3h
  - Blocked on DBA approval #repeat
  - [x] Write migration ~3h done:2024-03-10

- [ ] Daily standup @team planned:2024-03-15T09:00 repeat:weekdays ~15m

## Frontend +Frontend #routine

- [ ] Complete UI redesign @bob due:2024-03-20 ~10h
- [ ] Weekly design review @team repeat:weekly ~1h
```

**What's happening here?**

| Element | Meaning |
|---------|---------|
| `# Acme Project +Acme #work` | Top-level metadata (all tasks inherit!) |
| `(A)` | Priority |
| `planned:2024-03-10` | Planned start date |
| `done:2024-03-10` | Completion date |
| `@alice` | Assigned to Alice |
| `+Backend` | Section project (combines with `+Acme`) |
| `#critical` | Section tag (adds to `#work`) |
| `due:2024-03-15` | Deadline |
| `~8h` | Time estimate |
| `repeat:weekdays` | Recurring task |
| Indented `- [ ]` | Subtask |
| Indented `- text` | Note (no brackets) |

### Inheritance Magic

Put metadata on ANY section, and all tasks inherit it:

```markdown
# Acme Project +Acme #work

## Database Maintenance +Database #critical

- [ ] Optimize indexes @alice
- [ ] Run vacuum @bob
```

Both tasks automatically get: `+Acme/Database`, `#work`, and `#critical`

**Every task in the file inherits from the top-level header!**

---

## Features at a Glance

### Task States

| Symbol | Meaning | Example |
|--------|---------|---------|
| `[ ]` | Open | `- [ ] Fix login bug` |
| `[.]` | In Progress | `- [.] Working on feature` |
| `[x]` | Done | `- [x] Update docs` |
| `[-]` | Cancelled | `- [-] Deprecated feature` |
| `[!]` | Blocked | `- [!] Awaiting API access` |

### Priorities

```markdown
- [ ] (A) Critical security fix
- [ ] (B) Routine maintenance
- [ ] (C) Nice to have
```

Use any value: `(A)`, `(1)`, `(urgent)`, `(P1)` — whatever makes sense.

### Dates and Times

```markdown
# Date fields use inline metadata
- [ ] Start project planned:2024-03-10
- [x] Complete report planned:2024-03-08 done:2024-03-10

# With times
- [ ] Morning standup planned:2024-03-15T09:00 due:2024-03-15T09:30

# With timezones
- [ ] Team call (EST) planned:2024-03-15T09:00-05:00
```

### Recurring Tasks

```markdown
- [ ] Daily standup @team repeat:weekdays
- [ ] Weekly status report repeat:weekly
- [ ] Monthly review repeat:"first monday of month"
```

When you mark a recurring task done, it automatically creates the next one.

### Subtasks and Notes

```markdown
- [ ] Launch new feature @alice
  - [ ] Write documentation
  - [ ] Create tests
  - [x] Design mockups
  - Remember to update changelog
  - Check with legal team #repeat
```

Subtasks have state markers (`[ ]`). Notes are just text after a dash.

Notes with `#repeat` are copied when recurring tasks generate new instances.

### Multiple Files

**Main: project-todo.md**

```markdown
# Acme Project +Acme #work

## Backend +Backend #critical

[API Team](backend/api.md)
[Database Team](backend/database.md)
```

**Linked: backend/api.md**

```markdown
# API Tasks

- [ ] Add rate limiting @alice
- [ ] Update authentication @bob
```

All API tasks automatically get: `+Acme/Backend`, `#work`, `#critical`

### Custom Metadata

Add whatever you need:

```markdown
- [ ] Fix bug type:urgent ticket:ENG-4701 url:https://jira.example.com/ENG-4701
- [ ] Research task notes:"Check latest specs" priority:high
```

---

## Documentation

| Document | What's Inside |
|----------|---------------|
| [Specification](spec/specification.md) | Core format specification |
| [Detailed Specs](spec/) | Deep-dive on specific features |
| [Format Comparison](docs/format-comparison.md) | How we compare to todo.txt, xit, etc. |

---

## Philosophy

**Simple by default, powerful when you need it.**

Start with basic checkboxes. Add structure as your project grows. Use metadata inheritance to stay DRY. Version control everything.

**Plain text is forever.** Your todos will outlive any app.

**Git-native.** Treat your tasks like code. Branch, merge, diff, blame — it all just works.

**Markdown everywhere.** Looks good on GitHub, renders in VSCode, readable in terminal, works in any editor.

---

## Quick Reference

### Minimal Task

```markdown
- [ ] Task description
```

### Full Featured

```markdown
- [ ] (A) Task @user +project #tag planned:2024-03-10 due:2024-03-15 ~2h type:bug
  - [ ] Subtask
  - Note with context
```

### Section with Inheritance

```markdown
# My Project +project #tag type:value

## Section Name +section-project

- [ ] Task inherits: +project/section-project, #tag, type:value
```

### States

- `[ ]` = Open
- `[.]` = In Progress
- `[x]` = Done
- `[-]` = Cancelled
- `[!]` = Blocked

---

## Implementation Status

| Component | Status |
|-----------|--------|
| Specification | ✅ v2.0.1 |
| Reference Parser | ⏳ Not yet implemented |
| Editor Support | ⏳ Not yet implemented |

Want to help? We'd love contributions for parsers, editor plugins, or CLI tools.

---

## Contributing

This is a format specification. We welcome:

- Spec improvements and clarifications
- Parser implementations (Python, JavaScript, Go, Rust...)
- Editor plugins (VSCode, Vim, Emacs...)
- CLI tools
- Example files
- Bug reports and suggestions

### Development Setup

```bash
# Install pre-commit hooks (requires Python)
pip install pre-commit
pre-commit install --hook-type pre-commit --hook-type commit-msg

# Run checks manually
pre-commit run --all-files
```

### Commit Convention

This project uses [Conventional Commits](https://www.conventionalcommits.org/):

```bash
feat: add new feature        # → minor version bump
fix: fix a bug               # → patch version bump
docs: update documentation   # → patch version bump
chore: maintenance task      # → no release
```

---

## Credits

Inspired by the best parts of:

- [todo.txt](https://github.com/todotxt/todo.txt) — The OG plain-text format
- [todo.md](https://github.com/todomd/todo.md) — Markdown task lists
- [xit](https://github.com/jotaen/xit) — Minimalist text format

Specification format influenced by:

- [AISD](https://github.com/rickgemignani/AISD) — AI Small Docs
- [RFC 2119](https://www.rfc-editor.org/rfc/rfc2119) — Requirement levels

---

## License

MIT License — see [LICENSE](LICENSE) file for details.

**TL;DR:** Use it however you want. Build tools, sell products, fork it, whatever. No strings attached.
