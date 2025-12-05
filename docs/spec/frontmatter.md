# YAML Front Matter

Detailed documentation for TaskMark YAML front matter. See [specification.md](../specification.md) for the core spec.

---

## Position and Syntax

| Constraint | Rule |
|-----------|------|
| Position | MUST appear at file start |
| Delimiters | MUST be enclosed between `---` lines |
| Syntax | MUST be valid YAML |
| Invalid YAML | MUST warn, MUST NOT fail parsing |
| Unknown fields | MUST be ignored |

---

## Supported Fields

| Field | Type | Constraint | Purpose | Example Values |
|-------|------|-----------|---------|----------------|
| `locale` | String | OPTIONAL | Locale for dateparser/recurrent | `en_US`, `fr_FR`, `de_DE` |
| `timezone` | String | OPTIONAL | Default timezone for datetime fields | `America/New_York`, `Europe/Paris`, `UTC` |
| `date_format` | String | OPTIONAL | Output date format (strftime pattern) | `%Y-%m-%d`, `%d/%m/%Y`, `%B %d, %Y` |

---

## Field Behavior

| Field | Input Parsing | Output Formatting |
|-------|--------------|-------------------|
| `locale` | Used by dateparser for natural language | If set alone, uses locale's default date format |
| `timezone` | Applied to dates without explicit timezone | Applied when serializing dates |
| `date_format` | Not used for parsing | Overrides locale default when both are set |

**Default Output Format:** ISO 8601 (`YYYY-MM-DD`) when no `locale` or `date_format` is set.

---

## Processing Order

1. Parse YAML front matter (if present)
2. Extract `locale`, `timezone`, and `date_format` values
3. Apply to all subsequent date/time parsing in THIS FILE ONLY

---

## File Scope

| Rule | Constraint |
|------|-----------|
| Scope | Front matter settings apply ONLY to the current file |
| Linked files | Imported files via `[Link](file.md)` retain their own front matter |
| No inheritance | Parent file front matter does NOT cascade to linked files |
| Default behavior | Files without front matter use system locale/timezone and ISO 8601 output |

---

## Examples

### Basic Usage

```markdown
---
locale: en_US
timezone: America/New_York
---

# TODO

## Meetings
- [ ] 2024-03-18T09:00 Weekly standup repeat:"every monday at 9am"
- [ ] 2024-04-01 Monthly review repeat:"first day of month"

## Tasks
- [ ] (A) 2024-03-10 Start development @alice due:2024-03-15T17:00
```

**Result:** All datetimes without explicit timezone use `America/New_York` (EST/EDT).

### Custom Date Format

```markdown
---
locale: en_GB
date_format: "%d/%m/%Y"
timezone: Europe/London
---

# TODO

- [ ] 10/03/2024 Project kickoff @alice
- [x] 01/03/2024 05/03/2024 Complete requirements @bob
```

**Result:** Dates are output in day/month/year format. The `date_format` overrides the locale default.

### Multi-File with Different Front Matter

**main.md:**

```markdown
---
timezone: America/New_York
date_format: "%Y-%m-%d"
---

# TODO +Project

## Engineering +Engineering
[UK Team](uk-team.md)
```

**uk-team.md:**

```markdown
---
timezone: Europe/London
date_format: "%d/%m/%Y"
---

# UK Team Tasks

- [ ] 15/03/2024 Sprint planning @alice
```

**Result:** Each file uses its own front matter. The UK team file displays dates in `dd/mm/yyyy` format with London timezone, independent of the parent file's settings.
