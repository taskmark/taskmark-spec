# Subtasks

Indented tasks under a parent. See [specification.md](specification.md).

---

## Definition

| Property | Rule |
|----------|------|
| Nesting | 1 level only |
| `+project` | FORBIDDEN |
| `repeat:` | FORBIDDEN |
| Identity | Component of parent task |

---

## Detection

| Pattern | Result |
|---------|--------|
| Indented `- [ ]` | Subtask (open) |
| Indented `- [.]` | Subtask (in progress) |
| Indented `- [x]` | Subtask (done) |
| Indented `- [-]` | Subtask (cancelled) |
| Indented `- [!]` | Subtask (blocked) |
| Indented `- text` | Note |

**Indentation:** Whitespace > 0

---

## Properties

| Property | Behavior |
|----------|----------|
| `@assignee` | Propagates to parent |
| `#tag` | Propagates to parent |
| `#repeat` | RESERVED (copied on recurrence) |
| `~estimate` | Validated against parent |
| Dates | Independent |
| Priority | Sorting only |

---

## Propagation

Subtask `@assignee` and `#tag` add to parent during parsing.

```markdown
- [ ] Task @alice
  - [ ] Subtask @bob #urgent
# Parent has: @alice @bob #urgent
```

---

## Inheritance

Subtasks inherit section + parent metadata:

| Type | Behavior |
|------|----------|
| Projects | Hierarchical join |
| Tags | Additive |
| Assignees | Additive |
| Estimate | NOT inherited |

---

## Validation

| Condition | Level |
|-----------|-------|
| Subtask estimates > parent | WARNING |
| Subtask `planned` < parent `planned` | WARNING |
| Subtask `due` > parent `due` | WARNING |
| Parent no estimate, subtasks have | INFO |

Values kept as-is after warning.

---

## Completion

| Event | Behavior |
|-------|----------|
| Parent marked `[x]` | Subtasks unchanged |
| All subtasks marked `[x]` | Parent unchanged |
| Subtask marked `[x]` | Sets `done:` datetime |

---

## Serialization

| Order | Sort |
|-------|------|
| 1 | Priority ascending (A < B < none) |
| 2 | Original line number |

---

## Invalid Nesting

| Error | Action |
|-------|--------|
| Sub-subtask | Warn, flatten to 1 level |
