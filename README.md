# TaskMark

**Plain-text todos that work everywhere.** Write tasks in Markdown, track them in git, and never lose your todos again.

**Status:** Draft v1.0.0

---

## Why TaskMark?

Your todos should be as simple as your code:

- **Plain text** - Works in any editor, forever
- **Git-friendly** - Version control your tasks like your code
- **Markdown-based** - Looks good everywhere (GitHub, VSCode, terminal)
- **Organized** - Sections with metadata inheritance
- **Powerful** - Recurring tasks, subtasks, priorities, estimates

---

## Quick Start

### The Basics

```markdown
# TODO

- [ ] Inspect warp nacelles @geordi
- [x] 2366-03-10 2366-03-10 Calibrate sensor array @data
- [ ] Crew physicals @crusher due:2366-03-15
```

That's it. You're done. Everything else is optional.

### Add Some Structure

```markdown
# TODO +Enterprise #starfleet

## Engineering Operations +Engineering #critical

- [ ] (A) 2366-03-10 Repair warp drive @geordi due:2366-03-15 ~8h
  - [ ] Replace magnetic constrictors ~2h
  - [ ] Recalibrate plasma injectors ~3h
  - [x] 2366-03-09 2366-03-10 Test at warp 5 ~3h

- [ ] 2366-03-15T09:00 Daily standup @geordi repeat:weekdays ~15m

## Medical Operations +Medical #routine

- [ ] Complete crew physicals @crusher due:2366-03-20 ~10h
- [ ] Weekly medical staff meeting @crusher repeat:weekly ~1h
```

**What's happening here?**
- `# TODO +Enterprise #starfleet` = Top-level metadata (all tasks inherit this!)
- `(A)` = Priority
- `2366-03-10` = Planned start date
- `2366-03-10 2366-03-10` = Planned and done dates
- `@geordi` = Assigned to Geordi
- `+Engineering` = Section project (combines with `+Enterprise`)
- `#critical` = Section tag (adds to `#starfleet`)
- `due:2366-03-15` = Deadline
- `~8h` = Time estimate
- `repeat:weekdays` = Recurring task
- Indented = Subtasks

### Inheritance Magic

Put metadata on ANY section (even the top one!), and all tasks inherit it:

```markdown
# TODO +Enterprise #starfleet

## Warp Core Maintenance +WarpCore #critical

- [ ] Inspect dilithium chamber @barclay
- [ ] Run diagnostics @geordi
```

Both tasks automatically get: `+Enterprise/WarpCore`, `#starfleet`, and `#critical`

**Every task in the file inherits from `# TODO`!**

---

## Features

### Task States

| Symbol | Meaning | Example |
|--------|---------|---------|
| `[ ]` | Open task | `- [ ] Fix warp drive` |
| `[x]` | Done | `- [x] Calibrate sensors` |
| `[-]` | Cancelled | `- [-] Abandon old protocol` |
| `[!]` | Blocked | `- [!] Awaiting Starfleet approval` |

### Priorities

```markdown
- [ ] (A) Emergency warp core repair
- [ ] (B) Routine maintenance
- [ ] (C) Optional upgrades
```

Use any value: `(A)`, `(1)`, `(urgent)`, `(P1)` - whatever makes sense to you.

### Dates and Times

```markdown
# Simple dates
- [ ] 2366-03-10 Start mission
- [x] 2366-03-08 2366-03-10 Complete report

# With times
- [ ] 2366-03-15T09:00 Morning briefing due:2366-03-15T09:30

# With timezones
- [ ] 2366-03-15T09:00-05:00 Team call (EST)
- [ ] 2366-03-15T15:00+01:00 European meeting
```

### Recurring Tasks

```markdown
- [ ] Daily standup @team repeat:weekdays
- [ ] Weekly status report repeat:weekly
- [ ] Monthly review repeat:"first monday of month"
```

When you mark a recurring task done, it automatically creates the next one.

### Subtasks

```markdown
- [ ] Launch away mission @riker
  - [ ] Brief away team
  - [ ] Check transporter
  - [x] Pack emergency supplies
```

One level only. Keep it simple.

### Multiple Files

**Main: enterprise-todo.md**
```markdown
# TODO +Enterprise #starfleet

## Engineering +Engineering #critical

[Warp Core Team](engineering/warp-core.md)
[Transporter Team](engineering/transporters.md)

## Medical +Medical #routine

[Medical Staff](medical/staff-tasks.md)
```

**Linked: engineering/warp-core.md**
```markdown
# Warp Core Maintenance

- [ ] Inspect dilithium chamber @barclay
- [ ] Calibrate antimatter flow @geordi
```

All warp core tasks automatically get: `+Enterprise/Engineering`, `#starfleet`, `#critical`

### Custom Metadata

Add whatever you need:

```markdown
- [ ] Fix bug type:urgent ticket:ENG-4701 url:https://starfleet.fed/tickets/4701
- [ ] Research task notes:"Check latest specs" priority:high
```

---

## Real Examples

Check out the [examples directory](examples/) for complete todo files:

- **[Simple Daily Operations](examples/simple-daily-ops.md)** - Basic task tracking
- **[Medium Mission Planning](examples/medium-mission.md)** - With priorities and estimates
- **[Complex Starbase Operations](examples/complex-starbase-ops.md)** - Full lifecycle tracking with timezones

All examples use Star Trek TNG scenarios because everything's better with Star Trek.

---

## Documentation

| Document | What's Inside |
|----------|--------------|
| [Specification](docs/specification.md) | Complete technical specification |
| [Format Comparison](docs/format-comparison.md) | How we compare to todo.txt, xit, etc. |

---

## Philosophy

**Simple by default, powerful when you need it.**

Start with basic checkboxes. Add structure as your project grows. Use metadata inheritance to stay DRY. Version control everything.

**Plain text is forever.** Your todos will outlive any app.

**Git-native.** Treat your tasks like code. Branch, merge, diff, blame - it all just works.

**Markdown everywhere.** Looks good on GitHub, renders in VSCode, readable in terminal, works in any editor.

---

## Quick Reference

### Basic Task
```markdown
- [ ] Task description
```

### Everything
```markdown
- [ ] (A) 2366-03-10 Task @user +project #tag due:2366-03-15 ~2h type:bug
  - [ ] Subtask
```

### Section with Inheritance
```markdown
# TODO +project #tag type:value

## Section Name +section-project

- [ ] Task inherits: +project/section-project, #tag, type:value
```

### Recurring
```markdown
- [ ] Task repeat:weekdays due:2366-03-15
```

### States
- `[ ]` = Open
- `[x]` = Done
- `[-]` = Cancelled
- `[!]` = Blocked

---

## Implementation Status

**Specification:** ✅ Draft v1.0.0 (Complete)
**Reference Parser:** ⏳ Not yet implemented
**Editor Support:** ⏳ Not yet implemented

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

---

## Credits

Inspired by the best parts of:
- [todo.txt](https://github.com/todotxt/todo.txt) - The OG plain-text format
- [todo.md](https://github.com/todomd/todo.md) - Markdown task lists
- [xit](https://github.com/jotaen/xit) - Minimalist text format

Specification format influenced by:
- [AISD](https://github.com/rickgemignani/AISD) - AI Small Docs
- [RFC 2119](https://www.rfc-editor.org/rfc/rfc2119) - Requirement levels

---

## License

MIT License - see [LICENSE](LICENSE) file for details.

**TL;DR:** Use it however you want. Build tools, sell products, fork it, whatever. No strings attached.

---

**Make it so.** 🖖
