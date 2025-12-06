# Serialization

Task output format. See [specification.md](specification.md).

---

## Task Order

```
- [STATE] (PRIORITY) Description +projects @assignees #tags ~estimate dates repeat: custom:
  - Subtasks (sorted)
  - Notes (original order)
```

---

## Metadata Order

| Position | Component |
|----------|-----------|
| 1 | `+projects` |
| 2 | `@assignees` |
| 3 | `#tags` |
| 4 | `~estimate` |
| 5 | Dates: `created:`, `planned:`, `started:`, `paused:`, `due:`, `done:` |
| 6 | `repeat:` |
| 7 | Custom keys (alphabetical) |

---

## Sorting

| Element | Order |
|---------|-------|
| Subtasks | Priority ascending, then line number |
| Notes | After subtasks, original order |
| Custom keys | Alphabetical |

---

## Date Output

| Scenario | Format |
|----------|--------|
| No frontmatter TZ | Preserve explicit TZ |
| Frontmatter TZ set | Convert, strip TZ |
| Date only | `YYYY-MM-DD` |
| Datetime | `YYYY-MM-DDTHH:MM` |

---

## Values

| Condition | Format |
|-----------|--------|
| Has spaces | `key:"value with spaces"` |
| Simple | `key:value` |
| Multiple | Space-separated |

---

## Whitespace

| Element | Rule |
|---------|------|
| Description | Trim |
| Between tokens | Single space |
| Indentation | 2 spaces or preserve |
| Empty notes | Skip |
