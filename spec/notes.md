# Notes

Plain text annotations under tasks. See [specification.md](specification.md) for core spec.

---

## Definition

| Property | Value |
|----------|-------|
| Content | Plain text (no metadata parsing) |
| State | None |
| Attachment | Parent task only (not subtasks) |
| Special | `#repeat` tag (word boundary match) |

---

## Detection

A note is an indented line starting with `-` that is NOT a valid subtask.

| Pattern | Result |
|---------|--------|
| `- [ ] text` | Subtask |
| `- [.] text` | Subtask |
| `- [x] text` | Subtask |
| `- [-] text` | Subtask |
| `- [!] text` | Subtask |
| `- text` | Note |
| `- [z] text` | Note (invalid state) |

**Indentation:** Any whitespace > 0 under a task.

**Invalid states:** `- [anything]` where `anything` is not ``, `.`, `x`, `-`, `!` is treated as a note. Preserved verbatim on serialization.

---

## Multiline Notes

```markdown
- [ ] Task
  - Note line one
    continuation (more indented, no dash)
    more continuation
  - New note (dash at note indent)
```

| Rule | Behavior |
|------|----------|
| Continuation | More indented than note, no `-` prefix |
| Ends at | Same/less indent, or line starting with `-` |
| Blank lines | End the note |

---

## The `#repeat` Tag

Notes containing `#repeat` are copied when a recurring task completes.

**Matching:** Word boundary required. Matches `#repeat` but not `#repeated` or `foo#repeat`.

```markdown
# Before:
- [ ] Weekly review repeat:weekly
  - Check metrics #repeat
  - Personal reminder

# After marking [x]:
- [ ] Weekly review repeat:weekly planned:2024-03-22
  - Check metrics #repeat
- [x] Weekly review done:2024-03-15T14:30
  - Check metrics #repeat
  - Personal reminder
```

---

## Serialization

| Order | Content |
|-------|---------|
| 1 | Subtasks (by priority, then original order) |
| 2 | Notes (original order) |

Notes reorder after subtasks but preserve relative order among themselves.

---

## Error Handling

| Condition | Action |
|-----------|--------|
| Empty note (`-`) | Ignore |
| No parent task | Treat as markdown text |
| Under subtask | Attach to parent task instead |
