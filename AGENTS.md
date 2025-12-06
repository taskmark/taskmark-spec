# AI Agents Guide — TaskMark

Documentation for AI agents working on the TaskMark project.

**Version:** 1.2.0

---

## Documentation Map

| Document | Purpose |
|----------|---------|
| [specification.md](spec/specification.md) | Core format specification |
| [examples.md](spec/examples.md) | Format examples |
| [frontmatter.md](spec/frontmatter.md) | YAML configuration |
| [subtasks.md](spec/subtasks.md) | Subtask rules |
| [notes.md](spec/notes.md) | Note syntax |
| [recurrence.md](spec/recurrence.md) | Recurring tasks |
| [mutation.md](spec/mutation.md) | Task mutation rules |
| [serialization.md](spec/serialization.md) | Output formatting |
| [compatibility.md](docs/compatibility.md) | Format conversion |
| [libraries.md](docs/libraries.md) | Implementation libraries |

---

## Commit Format

```
<type>: <description in imperative mood>

Co-Authored-By: Claude <noreply@anthropic.com>
```

| Type | Use Case |
|------|----------|
| `docs:` | Documentation changes |
| `feat:` | New features |
| `fix:` | Bug fixes |
| `refactor:` | Code restructuring |
| `test:` | Test additions/changes |
| `chore:` | Maintenance tasks |

---

## Project Structure

```
taskmark-spec/
├── README.md                    # User introduction
├── AGENTS.md                    # This file
├── spec/                        # Core specification
│   ├── specification.md         # Main spec
│   └── *.md                     # Detailed specs
├── docs/                        # Implementation docs
│   ├── format-comparison.md     # Format comparison
│   ├── compatibility.md         # Format conversion
│   └── libraries.md             # Recommended libraries
└── tests/                       # Golden test files
```

---

## Quick Syntax Reference

### Task States

| Symbol | State | Creates Recurrence |
|--------|-------|-------------------|
| `[ ]` | Open | No |
| `[.]` | In Progress | No |
| `[x]` | Done | Yes (if `repeat:`) |
| `[-]` | Cancelled | No |
| `[!]` | Blocked | No |

### Task Pattern

```markdown
- [STATE] (PRIORITY) Description @assignee +project #tag ~estimate due:DATE repeat:PATTERN key:value
  - [STATE] Subtask (inherits parent metadata)
  - Note text (no brackets)
```

### Inheritance

```markdown
# Project +Acme #work @team
## Section +Backend #critical
- [ ] Task inherits: +Acme/Backend, #work, #critical, @team
```

| Type | Behavior |
|------|----------|
| Projects (`+`) | Hierarchical: `+A` + `+B` = `+A/B` |
| Tags (`#`) | Additive |
| Assignees (`@`) | Additive |
| Key-value | Child overrides parent |

See [specification.md](spec/specification.md) for complete syntax.

---

## AI Agent Workflow

### Creating Todo Files

1. Start with `#` header + basic tasks
2. Add section headers for organization
3. Put metadata on sections (inheritance)
4. Add priorities, dates, estimates where needed

### Updating Tasks

1. Read file first
2. Preserve existing formatting
3. Check for `repeat:` implications
4. Use inline metadata for dates (`planned:`, `due:`, `done:`)

### Common Pitfalls

| Pitfall | Solution |
|---------|----------|
| Redundant project on task | Let task inherit from section |
| Deep subtask nesting | Flatten to 1 level |
| Inline file links | Put links on standalone lines |
| Non-ISO dates | Use `YYYY-MM-DD` format |
| Missing quotes | Use `key:"value with spaces"` |

See [examples.md](spec/examples.md) for patterns.

---

## Error Handling

Parser MUST NOT fail unless file is unparseable.

| Error | Action |
|-------|--------|
| Malformed date | Preserve as string |
| Invalid state | Treat as note |
| Sub-subtask | Warn, flatten |
| `+project` on subtask | Warn, ignore |
| `repeat:` on subtask | Warn, ignore |

See [specification.md](spec/specification.md) for complete error handling.

---

## Implementation Status

| Component | Status |
|-----------|--------|
| Specification | ✅ v1.2.0 |
| Reference Parser | ⏳ Not started |
| VSCode Extension | ⏳ Not started |
| CLI Tool | ⏳ Not started |
