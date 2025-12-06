# TaskMark Format Specification

**Version:** 1.2.0
**Encoding:** UTF-8
**Extension:** `.md`

---

## File Structure

| Element | Rule |
|---------|------|
| YAML front matter | OPTIONAL, between `---` |
| Headers | `#`, `##`, `###` for sections |
| File links | `[text](file.md)` on standalone line |

See [frontmatter.md](frontmatter.md).

---

## Task States

| Symbol | State | On Completion |
|--------|-------|---------------|
| `[ ]` | Open | — |
| `[.]` | In Progress | — |
| `[x]` | Done | Creates recurrence if `repeat:` |
| `[-]` | Cancelled | — |
| `[!]` | Blocked | — |

`[X]` = `[x]` (case-insensitive).

---

## Task Pattern

```
- [STATE] (PRIORITY) Description +project @assignee #tag ~estimate dates repeat: custom:
```

| Component | Pattern | Required |
|-----------|---------|----------|
| State | `- [X]` | Yes |
| Priority | `(VALUE)` | No |
| Description | Plain text | Yes |

---

## Metadata

| Type | Pattern | Example |
|------|---------|---------|
| Project | `+name[/sub]` | `+Acme/Backend` |
| Assignee | `@username` | `@alice` |
| Tag | `#tag` | `#critical` |
| Estimate | `~NUM[UNIT]` | `~2h`, `~30min` |
| Created | `created:DATETIME` | `created:2024-03-01` |
| Planned | `planned:DATETIME` | `planned:2024-03-10` |
| Started | `started:DATETIME` | `started:2024-03-10` |
| Paused | `paused:DATETIME` | `paused:2024-03-11` |
| Due | `due:DATETIME` | `due:2024-03-15` |
| Done | `done:DATETIME` | `done:2024-03-12` |
| Repeat | `repeat:PATTERN` | `repeat:weekly` |
| Custom | `key:value` | `type:bug` |

All tokens case-insensitive except values.

---

## Character Sets

| Token | Valid Characters |
|-------|------------------|
| `@assignee` | `a-zA-Z0-9_-` |
| `+project` | `a-zA-Z0-9_-./` |
| `#tag` | `a-zA-Z0-9_-` |
| Metadata key | `a-zA-Z0-9_-` |
| Unquoted value | Non-whitespace |
| Quoted value | Any (`key:"value"`) |

---

## Estimate Units

| Unit | Aliases |
|------|---------|
| Hours | `h`, `hour`, `hours` |
| Minutes | `m`, `min`, `minute`, `minutes` |
| Days | `d`, `day`, `days` |

Decimals allowed (`~1.5h`). Serialize with shortest alias.

---

## Escaping

| Escape | Result |
|--------|--------|
| `\@` | Literal @ |
| `\+` | Literal + |
| `\#` | Literal # |
| `\:` | Literal : |
| `\\` | Literal \ |
| `\"` | Quote in double-quoted value |

---

## Dates

| Format | Example |
|--------|---------|
| ISO 8601 date | `2024-03-10` |
| ISO 8601 datetime | `2024-03-10T09:00` |
| With seconds | `2024-03-10T09:00:00` |
| With timezone | `2024-03-10T09:00-05:00` |
| Custom | Per `datetime_format` |

### Parsing Precedence

| Priority | Source |
|----------|--------|
| 1 | `datetime_format` from frontmatter |
| 2 | `locale` from frontmatter |
| 3 | ISO 8601 |
| 4 | System locale |
| 5 | Keep as string (WARNING) |

### Serialization

| Condition | Output |
|-----------|--------|
| `datetime_format` set | Custom format |
| `locale` set | Locale default |
| No frontmatter | ISO 8601 |

See [frontmatter.md](frontmatter.md).

---

## Subtasks

| Rule | Constraint |
|------|------------|
| Detection | Indented `- [STATE]` under task |
| Nesting | 1 level only |
| `+project` | FORBIDDEN |
| `repeat:` | FORBIDDEN |
| `@assignee`, `#tag` | Propagates to parent |
| `#repeat` | RESERVED for recurrence |

See [subtasks.md](subtasks.md).

---

## Notes

| Pattern | Result |
|---------|--------|
| `- [ ] text` | Subtask |
| `- text` | Note |
| `more text` | Continuation |

| Rule | Constraint |
|------|------------|
| Metadata | None (plain text) |
| `#repeat` | RESERVED for recurrence |

See [notes.md](notes.md).

---

## Inheritance

| Type | Behavior |
|------|----------|
| `+project` | Hierarchical join (`+A/B/C`) |
| `#tag` | Additive |
| `@assignee` | Additive |
| `key:value` | Child overrides parent |

Flows: `#` → `##` → `###` → task → subtask.

---

## File Links

| Rule | Constraint |
|------|------------|
| Syntax | `[text](path/to/file.md)` |
| Location | Standalone line only |
| Extension | MUST be `.md` |
| Inheritance | Linked tasks inherit section metadata |

---

## Recurrence

| Pattern | RRULE |
|---------|-------|
| `daily` | FREQ=DAILY |
| `weekly` | FREQ=WEEKLY |
| `weekdays` | BYDAY=MO,TU,WE,TH,FR |
| `monthly` | FREQ=MONTHLY |
| `"first monday"` | Natural language |

### On `[x]` Completion

| Old Task | New Task |
|----------|----------|
| State → `[x]` | State → `[ ]` |
| Add `done:` | Calculate dates |
| Remove `repeat:` | Keep `repeat:` |

See [recurrence.md](recurrence.md).

---

## Priority

| Condition | Sort |
|-----------|------|
| All numeric | `(1)` < `(2)` < `(10)` |
| Any non-numeric | `(A)` < `(B)` < `(C)` |
| Same priority | Line number |
| Multiple | Use first |

---

## Mutation

| Action | Allowed |
|--------|---------|
| Change state | Yes |
| Add/update dates | Yes |
| Add/remove subtasks, notes | Yes |
| Change inherited metadata | No |
| Move between sections | No |

See [mutation.md](mutation.md).

---

## Serialization Order

| Position | Component |
|----------|-----------|
| 1 | `+projects` |
| 2 | `@assignees` |
| 3 | `#tags` |
| 4 | `~estimate` |
| 5 | Dates: `created:`, `planned:`, `started:`, `paused:`, `due:`, `done:` |
| 6 | `repeat:` |
| 7 | Custom keys (alphabetical) |

Subtasks: priority ascending, then line number.
Notes: after subtasks, original order.

See [serialization.md](serialization.md).

---

## Error Handling

Parser MUST NOT fail unless file is unparseable.

| Error | Action | Level |
|-------|--------|-------|
| Malformed date | Preserve as string | WARNING |
| Invalid state | Treat as note | INFO |
| Multiple priorities | Use first | INFO |
| Unclosed quote | Treat as unquoted | WARNING |
| Sub-subtask | Flatten to 1 level | WARNING |
| Invalid YAML | Continue parsing | WARNING |
| `+project` on subtask | Ignore | WARNING |
| `repeat:` on subtask | Ignore | WARNING |

---

## Implementation

| Requirement | Constraint |
|-------------|------------|
| Encoding | UTF-8 only |
| Line endings | LF or CRLF |
| Case matching | Insensitive, preserve original |
| State symbols | `[ ]`, `[.]`, `[x]`, `[-]`, `[!]` only |

---

## Related Specs

| Spec | Content |
|------|---------|
| [frontmatter.md](frontmatter.md) | YAML, timezone, locale |
| [subtasks.md](subtasks.md) | Propagation, validation |
| [notes.md](notes.md) | Detection, `#repeat` |
| [mutation.md](mutation.md) | State changes, recurrence |
| [serialization.md](serialization.md) | Output format |
| [recurrence.md](recurrence.md) | RRULE, date calculation |
| [examples.md](examples.md) | Format examples |
| [compatibility.md](../docs/compatibility.md) | todo.txt, xit conversion |
