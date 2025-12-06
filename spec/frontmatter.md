# YAML Front Matter

Configuration for TaskMark files. See [specification.md](specification.md).

---

## Syntax

| Constraint | Rule |
|-----------|------|
| Position | File start |
| Delimiters | `---` lines |
| Invalid YAML | Warn, continue parsing |
| Unknown fields | Ignore |

---

## Fields

| Field | Type | Example |
|-------|------|---------|
| `locale` | String | `en_US`, `en_GB` |
| `timezone` | String | `America/New_York`, `UTC` |
| `datetime_format` | String | `%d/%m/%Y[ %H:%M]` |

---

## `datetime_format` Syntax

Strftime pattern with optional bracket syntax.

| Pattern | Output |
|---------|--------|
| `%d/%m/%Y` | `15/03/2024` |
| `%d/%m/%Y %H:%M` | `15/03/2024 09:00` |
| `%d/%m/%Y[ %H:%M]` | `15/03/2024` or `15/03/2024 09:00` |

**Brackets:** Content inside `[...]` included only if time component exists.

---

## Date Parsing Precedence

| Priority | Source |
|----------|--------|
| 1 | `datetime_format` from frontmatter |
| 2 | `locale` default format |
| 3 | ISO 8601 |
| 4 | System locale |
| 5 | Keep as string (emit WARNING) |

Mixed formats allowed per file. Unparseable dates preserved as strings.

---

## Serialization Output

| Condition | Format |
|-----------|--------|
| `datetime_format` set | Custom pattern |
| `locale` set | Locale default |
| No frontmatter | ISO 8601 |

---

## Scope

| Rule | Constraint |
|------|-----------|
| Applies to | Current file only |
| Linked files | Own frontmatter |
| Inheritance | None |
