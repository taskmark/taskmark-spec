# TaskMark Format Specification

**Version:** 1.0.0
**Format:** Markdown (.md)
**Encoding:** UTF-8 (REQUIRED)
**Line Endings:** LF (`\n`) or CRLF (`\r\n`)

---

## 1. File Structure

| Element | Constraint | Example |
|---------|-----------|---------|
| File extension | MUST be `.md` | `todo.md`, `TODO.md` |
| YAML front matter | OPTIONAL, between `---` delimiters | See §1.1 |
| First header | MUST be `# TODO` (or after front matter) | `# TODO` or `# TODO +project #tag` |
| Sections | MUST use `#` headers | `## Urgent`, `### Bugs` |
| Multiple files | ALLOWED in different directories | `project/todo.md`, `team/todo.md` |

### 1.1 YAML Front Matter

| Constraint | Rule |
|-----------|------|
| Position | MUST appear at file start |
| Delimiters | MUST be enclosed between `---` lines |
| Syntax | MUST be valid YAML |
| Invalid YAML | MUST warn, MUST NOT fail parsing |
| Unknown fields | MUST be ignored |

**Supported Fields:**

| Field | Type | Purpose |
|-------|------|---------|
| `locale` | String | Locale for dateparser/recurrent (`en_US`, `fr_FR`) |
| `timezone` | String | Default timezone (`America/New_York`, `UTC`) |
| `date_format` | String | Output date format (`%Y-%m-%d`, `%d/%m/%Y`) |

**See [spec/frontmatter.md](spec/frontmatter.md) for detailed field behavior, processing order, and file scope rules.**

---

## 2. Task Syntax

### 2.1 Task States

| State | Symbol | Meaning | Creates Recurrence |
|-------|--------|---------|-------------------|
| Open | `[ ]` | Pending/todo | No |
| Done | `[x]` | Completed | Yes (if `repeat:` present) |
| Cancelled | `[-]` | Cancelled/rejected | No |
| Blocked | `[!]` | Blocked/paused/on-hold | No |

**Case Sensitivity:** State markers are case-insensitive: `[X]` = `[x]`

### 2.2 Task Components

| Component | Pattern | Position | Required | Case Sensitive | Valid Range |
|-----------|---------|----------|----------|----------------|-------------|
| State | `- [X]` | Line start | Yes | No | `[ ]`, `[x]`, `[-]`, `[!]` |
| Priority | `(VALUE)` | After state | No | No | Any alphanumeric |
| Planned date | `YYYY-MM-DD[THH:MM[:SS][±HH:MM]]` | After priority | No | N/A | ISO 8601 datetime |
| Done date | `YYYY-MM-DD[THH:MM[:SS][±HH:MM]]` | After planned | No | N/A | ISO 8601 datetime |
| Description | Plain text | After dates/priority/state | Yes | Yes | 1-unlimited chars |
| Assignee | `@username` | In line | No | No | `a-zA-Z0-9_-` |
| Project | `+name[/sub]` | In line | No | No | `a-zA-Z0-9_-/` |
| Tag | `#tag` | In line | No | No | `a-zA-Z0-9_-` |
| Created date | `created:DATETIME` | In line | No | N/A | ISO 8601 datetime |
| Started date | `started:DATETIME` | In line | No | N/A | ISO 8601 datetime |
| Paused date | `paused:DATETIME` | In line | No | N/A | ISO 8601 datetime |
| Due date | `due:DATETIME` | In line | No | N/A | ISO 8601 datetime |
| Estimate | `~NUM[hmd]` | In line | No | N/A | Hours/minutes/days |
| Metadata | `key:value` or `key:"value"` | In line | No | Keys: No, Values: Yes | Any text |

**Processing Rule:** Position-based components MUST be parsed left-to-right before in-line components.

### 2.3 Date and Time Formats

**Accepted Formats:**

| Format | Pattern | Example | Timezone |
|--------|---------|---------|----------|
| Date only | `YYYY-MM-DD` | `2024-03-10` | Uses front matter `timezone` or local |
| Date + time | `YYYY-MM-DDTHH:MM` | `2024-03-10T09:00` | Uses front matter `timezone` or local |
| Date + time + seconds | `YYYY-MM-DDTHH:MM:SS` | `2024-03-10T09:00:00` | Uses front matter `timezone` or local |
| Date + time + timezone | `YYYY-MM-DDTHH:MM±HH:MM` | `2024-03-10T09:00-05:00` | Explicit timezone overrides default |
| Date + time + seconds + timezone | `YYYY-MM-DDTHH:MM:SS±HH:MM` | `2024-03-10T09:00:00-05:00` | Explicit timezone overrides default |

**Constraints:**

| Field | Min Value | Max Value | Rule |
|-------|-----------|-----------|------|
| Year | 0001 | 9999 | MUST be 4 digits |
| Month | 01 | 12 | MUST be 2 digits |
| Day | 01 | 31 | MUST be valid for month/year |
| Hour | 00 | 23 | MUST be 2 digits (24-hour format) |
| Minute | 00 | 59 | MUST be 2 digits |
| Second | 00 | 59 | MUST be 2 digits |
| Timezone offset | -12:00 | +14:00 | MUST match `±HH:MM` pattern |

**Invalid Date Handling:**

| Error | Action | Example |
|-------|--------|---------|
| Malformed date | Warn at `file:line`, ignore field | `due:2024-13-99` → warning + skip |
| Invalid day | Warn at `file:line`, ignore field | `2024-02-30` → warning + skip |
| Missing required component | Warn at `file:line`, ignore field | `2024-03` → warning + skip |

### 2.4 Character Sets

| Token Type | Pattern | Valid Characters | Notes |
|------------|---------|------------------|-------|
| Assignee | `@NAME` | `a-zA-Z0-9_-` | MUST follow whitespace |
| Project | `+NAME` or `+NAME/SUB` | `a-zA-Z0-9_-/` | `/` for hierarchy only |
| Tag | `#NAME` | `a-zA-Z0-9_-` | MUST NOT be section header |
| Metadata key | `KEY:` | `a-zA-Z0-9_-` | Before colon |
| Metadata value (unquoted) | `:VALUE` | Any non-whitespace | Until whitespace |
| Metadata value (quoted) | `:"VALUE"` or `:'VALUE'` | Any characters | Until closing quote |

### 2.5 Escaping

| Character | Escape Sequence | Result |
|-----------|----------------|--------|
| `@` | `\@` | Literal @ (not assignee) |
| `+` | `\+` | Literal + (not project) |
| `#` | `\#` | Literal # (not tag) |
| `:` | `\:` | Literal : (not key:value) |
| `\` | `\\` | Literal backslash |
| `"` | `\"` | Quote inside double-quoted value |
| `'` | `\'` | Quote inside single-quoted value |

### 2.6 Quoted Values

| Format | Pattern | Parsed Value | Whitespace Allowed |
|--------|---------|--------------|-------------------|
| Unquoted | `key:value` | `value` | No (stops at space) |
| Double quotes | `key:"value with spaces"` | `value with spaces` | Yes |
| Single quotes | `key:'value with spaces'` | `value with spaces` | Yes |

**Rules:**

| Constraint | Rule |
|-----------|------|
| Opening quote position | MUST immediately follow colon |
| Invalid spacing | `key: "value"` is INVALID |
| Closing quote | Terminates value |
| Quote stripping | Quotes MUST NOT be part of parsed value |
| Unclosed quote | Warn at `file:line`, treat as unquoted |

---

## 3. Priority

### 3.1 Priority Sorting

| Condition | Sort Method | Example |
|-----------|-------------|---------|
| All priorities numeric | Numeric ascending | `(1)` < `(2)` < `(10)` |
| Any priority non-numeric | Alphanumeric ascending | `(A)` < `(A1)` < `(B)` |
| Same priority | Line number ascending | First in file → first in list |
| Multiple priorities | Use first only | `(A) (B)` → priority `(A)` |

### 3.2 Priority Values

| Type | Examples | Sort Order |
|------|----------|------------|
| Letters | `(A)`, `(B)`, `(Z)` | Alphanumeric |
| Numbers | `(1)`, `(2)`, `(10)`, `(100)` | Numeric |
| Mixed | `(A1)`, `(P1)`, `(H)` | Alphanumeric |
| Case | `(a)` = `(A)` | Case-insensitive |

---

## 4. Recurrence

### 4.1 Recurrence Field

| Field | Pattern | Library | Example |
|-------|---------|---------|---------|
| Repeat pattern | `repeat:PATTERN` | [recurrent](https://github.com/kvh/recurrent) | `repeat:daily`, `repeat:weekdays` |

**Common Patterns:**

| Pattern | Syntax | Description | RRULE Equivalent |
|---------|--------|-------------|------------------|
| Daily | `repeat:daily` | Every day | `FREQ=DAILY` |
| Weekly | `repeat:weekly` | Every week | `FREQ=WEEKLY` |
| Bi-weekly | `repeat:every-2-weeks` | Every 2 weeks | `FREQ=WEEKLY;INTERVAL=2` |
| Monthly | `repeat:monthly` | Every month | `FREQ=MONTHLY` |
| Yearly | `repeat:yearly` | Every year | `FREQ=YEARLY` |
| Weekdays | `repeat:weekdays` | Monday-Friday | `FREQ=WEEKLY;BYDAY=MO,TU,WE,TH,FR` |
| Specific day | `repeat:every-tuesday` | Every Tuesday | `FREQ=WEEKLY;BYDAY=TU` |
| First of month | `repeat:first-monday-of-month` | First Monday each month | `FREQ=MONTHLY;BYDAY=1MO` |
| Last of month | `repeat:last-friday-of-month` | Last Friday each month | `FREQ=MONTHLY;BYDAY=-1FR` |

### 4.2 Recurrence Behavior

| Action | Old Task | New Task | Notes |
|--------|----------|----------|-------|
| Mark `[x]` with `repeat:` | State → `[x]`, add done date (2nd position), remove `repeat:` | State → `[ ]`, dates updated, keep `repeat:` | Creates new instance |
| Mark `[-]` with `repeat:` | State → `[-]`, keep `repeat:` | None | No new instance |
| Mark `[!]` with `repeat:` | State → `[!]`, keep `repeat:` | None | No new instance |
| Change `[x]` to `[ ]` | State → `[ ]`, keep done date | None | Manual cleanup required |

### 4.3 Date Calculation Algorithm

**Processing Steps:**

| Step | Action | Library |
|------|--------|---------|
| 1 | Parse `repeat:` pattern | recurrent |
| 2 | Extract RRULE + DTSTART | recurrent |
| 3 | Calculate next occurrence | python-dateutil.rrule |
| 4 | Calculate offset (if both planned & due exist) | pendulum |
| 5 | Apply offset to new dates | pendulum |

**Offset Preservation:**

| Scenario | Calculation | Example |
|----------|-------------|---------|
| Planned date only | `new_planned = next_occurrence` | Planned: 2024-03-18 |
| `due:` only | `new_due = next_occurrence` | `due:2024-03-18` |
| Both dates | `offset = due - planned`<br>`new_planned = next_occurrence`<br>`new_due = new_planned + offset` | 4-day offset preserved |

---

## 5. Sections and Inheritance

### 5.1 Section Headers

| Header Level | Pattern | Example |
|--------------|---------|---------|
| Top level | `# TODO` (required) | `# TODO +Acme #work` |
| Section | `## Section Name` | `## Backend +API #critical` |
| Subsection | `### Subsection Name` | `### Database +Maintenance` |

**All headers MAY include metadata (projects, tags, key-value pairs).**

### 5.2 Inheritance Rules

| Metadata Type | Behavior | Example |
|---------------|----------|---------|
| Projects (`+`) | Hierarchical (join with `/`) | `+A` + `+B` = `+A/B` |
| Tags (`#`) | Additive | `#api` + `#urgent` = both tags |
| Key-value | Override | `type:bug` overrides `type:feature` |

**Example:**

```markdown
# TODO +Acme #work

## Backend +API #critical

- [ ] Task +Database
```

**Task inherits:** `+Acme/API/Database`, `#work`, `#critical`

---

## 6. Subtasks

| Property | Constraint | Notes |
|----------|-----------|-------|
| Syntax | Indented task line | Any spaces/tabs |
| Nesting level | MUST be 1 level only | Tasks → subtasks, no deeper |
| Indentation | MUST be > 0 spaces/tabs | Any amount > 0 |
| Inheritance | Inherits ALL parent metadata | Task + section metadata |
| States | Independent of parent | Can differ from parent state |
| Projects | Hierarchical join | Parent `+A`, subtask `+B` → `+A/B` |
| Tags | Additive | Parent `#api`, subtask `#urgent` → both |
| Key-value | Override | Child overrides parent |

**Invalid Nesting:**

```markdown
- [ ] Task
  - [ ] Subtask (VALID)
    - [ ] Sub-subtask (INVALID - ignored or warned)
```

---

## 7. File Links

### 7.1 Link Syntax

| Element | Constraint | Example |
|---------|-----------|---------|
| Format | Standard Markdown link | `[Link Text](path/to/file.md)` |
| Location | MUST be standalone line | NOT inside task description |
| Link text | REQUIRED | MUST have descriptive text |
| Path type | Relative or absolute | See §7.2 |
| File extension | MUST be `.md` | Other extensions ignored |

### 7.2 Path Resolution

| Path Type | Pattern | Resolves To | Example |
|-----------|---------|-------------|---------|
| Relative | `file.md` | Same directory | `[Tasks](todo.md)` |
| Relative subdirectory | `dir/file.md` | Subdirectory from current | `[Team](team/tasks.md)` |
| Parent directory | `../file.md` | Parent directory | `[Main](../todo.md)` |
| Absolute | `/path/file.md` | From project root | `[Root](/project/todo.md)` |

### 7.3 Inheritance with Links

| Context | Rule | Example |
|---------|------|---------|
| Link in section | All linked tasks inherit section metadata | See example below |
| Link in subsection | All linked tasks inherit section + subsection | Hierarchical inheritance |
| Multiple links in section | Each link inherits same metadata | Applies to all |

**Example:**

```markdown
## Engineering +Enterprise #engineering type:maintenance

[Warp Core](team/warp.md)
[Transporters](team/transport.md)
```

**Result:** All tasks from both files inherit: `+Enterprise`, `#engineering`, `type:maintenance`

---

## 8. Parsing Rules

### 8.1 Detection Rules

| Element | Detection Pattern | Constraint |
|---------|------------------|-----------|
| Task | Line starts with `- [ ]`, `- [x]`, `- [-]`, `- [!]` | Case-insensitive state |
| Section | Line starts with `#` + space | MUST have space after `#` |
| Priority | First `(content)` after state | Rest ignored |
| Planned date | First ISO 8601 datetime after priority/state | Before description |
| Done date | Second ISO 8601 datetime | Only if first date exists |
| Assignee | `@` + word chars | Case-insensitive |
| Project | `+` + word chars (may include `/`) | Case-insensitive |
| Tag | `#` + word chars | Case-insensitive, NOT section |
| Created date | `created:DATETIME` | ISO 8601 format |
| Started date | `started:DATETIME` | ISO 8601 format |
| Paused date | `paused:DATETIME` | ISO 8601 format |
| Due date | `due:DATETIME` | ISO 8601 format |
| Estimate | `~` + digits + `h`/`m`/`d` | No spaces |
| Metadata (unquoted) | `key:value` | Until whitespace |
| Metadata (quoted) | `key:"value"` or `key:'value'` | Until closing quote |
| File link | `[text](path.md)` on standalone line | Not inline |

### 8.2 Error Handling

| Error Type | Action | Log Level | Example |
|------------|--------|-----------|---------|
| Malformed date | Warn at `file:line`, ignore field | WARNING | `due:2024-13-99` |
| Multiple priorities | Use first, ignore rest | INFO | `(A) (B)` → `(A)` |
| Invalid task state | Ignore line | INFO | `- [?] Task` |
| Empty task | Parse as valid | INFO | `- [ ]` |
| Leading/trailing spaces | Trim from description | NONE | `Task` → `Task` |
| Unclosed quote | Warn at `file:line`, treat as unquoted | WARNING | `desc:"unclosed` |
| Escaped quote | Remove backslash | NONE | `\"` → `"` |
| Invalid timezone | Warn at `file:line`, use default | WARNING | `T09:00+99:00` |
| Sub-subtask | Warn at `file:line`, treat as subtask | WARNING | 3+ level nesting |

### 8.3 Whitespace Handling

| Context | Rule | Example |
|---------|------|---------|
| Task description | Trim leading/trailing | `Task` → `Task` |
| Between tokens | Collapse to single space | `Task   @user` → `Task @user` |
| Indentation | Any amount = subtask | Spaces or tabs |
| Empty lines | Ignored | Not parsed |

---

## 9. Complete Syntax Pattern

```
- [STATE] (PRIORITY) [PLANNED] [DONE] Description @assignee +project #tag created:DT started:DT paused:DT due:DT repeat:PATTERN ~estimate key:value key:"quoted value"
  - [STATE] Subtask inherits all parent metadata
```

**Component Order:**

| Position | Component | Required | Pattern |
|----------|-----------|----------|---------|
| 1 | State marker | Yes | `- [ ]`, `- [x]`, `- [-]`, `- [!]` |
| 2 | Priority | No | `(VALUE)` |
| 3 | Planned date | No | ISO 8601 datetime |
| 4 | Done date | No | ISO 8601 datetime (requires planned) |
| 5 | Description | Yes | Plain text |
| 6+ | In-line metadata | No | Any order |

**In-line Metadata (any order):**

| Type | Examples |
|------|----------|
| Assignees | `@user1 @user2` |
| Projects | `+proj1 +proj2/sub` |
| Tags | `#tag1 #tag2` |
| Dates | `created:`, `started:`, `paused:`, `due:` |
| Recurrence | `repeat:PATTERN` or `repeat:"natural language"` |
| Estimate | `~2h`, `~30m`, `~3d` |
| Custom | `key:value`, `key:"quoted value"` |

---

## 10. Examples

### 10.1 Minimal

```markdown
# TODO
- [ ] Task
```

### 10.2 Full Featured

```markdown
# TODO +Acme #work

## Backend Operations +Backend #critical

- [ ] (A) 2024-03-10 Fix database connection @alice +Database due:2024-03-15T18:00 ~8h type:urgent
  - [ ] (B) 2024-03-10 Update connection pooling @alice ~2h
  - [x] (C) 2024-03-09 2024-03-10 Write migration @alice ~3h

- [ ] 2024-03-15T09:00 Daily status report @alice repeat:"weekdays at 9am" ~30m
```

**After Inheritance:**

- First task: `+Acme/Backend/Database`, `#work`, `#critical`
- Subtasks: Inherit parent + section + top-level metadata

**See [spec/examples.md](spec/examples.md) for date/time examples, YAML front matter examples, and multi-file examples.**

---

## 11. Library Stack

| Purpose | Library | Usage |
|---------|---------|-------|
| Natural language → datetime | [dateparser](https://dateparser.readthedocs.io/) | Parse "next Friday 3pm" |
| Natural language → recurrence | [recurrent](https://github.com/kvh/recurrent) | Parse "every Tuesday" into RRULE |
| Recurrence → dates | [python-dateutil](https://dateutil.readthedocs.io/) | Generate occurrence dates |
| Datetime wrapper | [pendulum](https://pendulum.eustace.io/) | Timezone-aware operations |

**See [spec/libraries.md](spec/libraries.md) for detailed library usage and processing pipeline.**

---

## 12. Common Conventions (NOT Core Spec)

| Key | Common Values | Type | Example |
|-----|---------------|------|---------|
| `type` | `bug`, `feature`, `docs`, `refactor`, `test` | Enum | `type:bug` |
| `epic` | Epic/initiative name | String | `epic:auth-v2` |
| `ticket` | Issue tracker ID | String | `ticket:PROJ-123` |
| `sprint` | Sprint number/name | String/Number | `sprint:24` |
| `milestone` | Release version | String | `milestone:v2.0` |
| `url` | Full URL | URL | `url:https://example.com/issue/123` |
| `version` | Version number | Semver | `version:1.2.3` |
| `priority` | Priority name | String | `priority:high` |
| `status` | Status name | String | `status:in-progress` |

**Note:** These are conventions only. Parsers MAY ignore or handle differently.

---

## 13. Implementation Requirements

| Requirement | Constraint | Error Handling |
|-------------|-----------|----------------|
| File encoding | MUST be UTF-8 | Fail on non-UTF-8 |
| Line endings | MUST accept LF and CRLF | Auto-detect |
| YAML front matter | MUST detect `---` delimiters | Warn on invalid YAML, continue |
| Locale handling | MUST use front matter `locale` if present | Default to system locale |
| Timezone handling | MUST use front matter `timezone` as default | Default to system timezone |
| Date parsing | MUST parse ISO 8601: `YYYY-MM-DD[THH:MM[:SS][±HH:MM]]` | Warn + skip invalid |
| Position-based dates | MUST recognize: 1st datetime = planned, 2nd = done | Parse left-to-right |
| Natural language dates | SHOULD use dateparser | Optional feature |
| Recurrence parsing | MUST use recurrent library | Fail if library unavailable |
| Recurrence calculation | MUST use python-dateutil.rrule | Fail if library unavailable |
| Datetime wrapper | SHOULD use pendulum | Optional but recommended |
| Malformed dates | MUST warn with `file:line:column` | Continue parsing |
| Case matching | MUST treat case-insensitive: `@User` = `@user` | Normalize to lowercase |
| Case preservation | MUST preserve original case for display | Store original |
| Priority parsing | MUST use first priority only | Ignore rest |
| Subtask depth | MUST reject > 1 level | Warn + treat as 1-level |
| State recognition | MUST recognize only: `[ ]`, `[x]`, `[-]`, `[!]` | Ignore other states |
| Quoted values | MUST support both `"` and `'` | Warn on unclosed |
| Quote escaping | MUST support `\"` and `\'` | Remove backslash |

---

## 14. Format Compatibility

TaskMark supports conversion to/from other task formats:

| Format | Full Compatibility | Notes |
|--------|-------------------|-------|
| todo.txt | → TaskMark: Yes | Contexts become tags |
| todo.md | → TaskMark: Yes | Add priority, metadata |
| xit | → TaskMark: Yes | Date headers become sections |

**See [spec/compatibility.md](spec/compatibility.md) for detailed conversion rules and examples.**

---

**End of Specification**
