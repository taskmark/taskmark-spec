# Recurrence

Recurring task patterns. See [specification.md](specification.md).

---

## Syntax

`repeat:PATTERN` or `repeat:"complex pattern"`

---

## Patterns

| Pattern | RRULE |
|---------|-------|
| `daily` | `FREQ=DAILY` |
| `weekly` | `FREQ=WEEKLY` |
| `every-2-weeks` | `FREQ=WEEKLY;INTERVAL=2` |
| `monthly` | `FREQ=MONTHLY` |
| `yearly` | `FREQ=YEARLY` |
| `weekdays` | `FREQ=WEEKLY;BYDAY=MO,TU,WE,TH,FR` |
| `every-tuesday` | `FREQ=WEEKLY;BYDAY=TU` |
| `first-monday-of-month` | `FREQ=MONTHLY;BYDAY=1MO` |
| `last-friday-of-month` | `FREQ=MONTHLY;BYDAY=-1FR` |

---

## Completion Behavior

| Action | Old Task | New Task |
|--------|----------|----------|
| Mark `[x]` | Done, remove `repeat:` | Created above, keep `repeat:` |
| Mark `[-]` | Cancelled, remove `repeat:` | None |
| Mark `[!]` | Blocked, keep `repeat:` | None |

---

## Date Calculation

| Step | Action |
|------|--------|
| 1 | Parse pattern to RRULE |
| 2 | Calculate next occurrence |
| 3 | Preserve offset (due - planned) |

---

## Offset Preservation

| Dates | Calculation |
|-------|-------------|
| Planned only | `new_planned = next` |
| Due only | `new_due = next` |
| Both | `offset = due - planned`, apply to new |

---

## `#repeat` Items

| Element | With `#repeat` | Without |
|---------|----------------|---------|
| Subtask | Copied | Not copied |
| Note | Copied | Not copied |

---

## Errors

| Case | Action |
|------|--------|
| Invalid pattern | Warn, treat as non-recurring |
| No DTSTART | Use `planned:` or current date |
