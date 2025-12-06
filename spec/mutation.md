# Mutation Rules

Task update behavior. See [specification.md](specification.md).

---

## Allowed Actions

| Action | Allowed |
|--------|---------|
| Change state | Yes |
| Add/update dates | Yes |
| Add/remove subtasks, notes | Yes |
| Change priority | Yes |
| Add/remove assignees, tags | Yes |

---

## Forbidden Actions

| Action | Error |
|--------|-------|
| Change inherited metadata | `CannotMutateInheritedProperty` |
| Move task between sections | `CannotMoveTask` |
| Modify section headers | N/A |
| Modify linked file frontmatter | N/A |

---

## State Change Effects

| Transition | Auto-set Field |
|------------|----------------|
| → `[.]` | `started:` (if null) |
| → `[!]` | `paused:` (always) |
| → `[x]` | `done:`, `planned:` (if null) |
| → `[-]` | None |

All state transitions are valid.

---

## Recurring Task Completion

When task with `repeat:` marked `[x]`:

**New task (above):**

| Property | Value |
|----------|-------|
| State | `[ ]` |
| Dates | Calculated from pattern |
| `repeat:` | Preserved |
| `#repeat` items | Copied |

**Completed task:**

| Property | Value |
|----------|-------|
| State | `[x]` |
| `done:` | Current datetime |
| `repeat:` | Removed |
| All items | Kept |

---

## Date Normalization

On mutation, dates normalize to frontmatter format:

| Setting | Output |
|---------|--------|
| `datetime_format` | Custom pattern |
| `locale` only | Locale default |
| None | ISO 8601 |

---

## Processing Order

| Step | Action |
|------|--------|
| 1 | Process BOTTOM to TOP |
| 2 | Reserialize in place |

---

## Multi-File

| File | Behavior |
|------|----------|
| Parent | Own frontmatter |
| Linked | Own frontmatter |
| Inheritance | Metadata flows down |
| Frontmatter | Does NOT flow |
