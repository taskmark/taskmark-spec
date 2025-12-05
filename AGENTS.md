# AI Agents Guide — TaskMark

AI-authored documentation for AI agents working on the TaskMark project.

**Format:** AISD-inspired
**Audience:** Claude Code, Cursor, and other AI coding assistants
**Last Updated:** 2025-12-05

---

## Commit Format

Follow conventional commits pattern with type prefixes:

| Type | Use Case | Example |
|------|----------|---------|
| `docs:` | Documentation changes | `docs: update specification for recurrence fields` |
| `feat:` | New features | `feat: add blocked state [!] to task syntax` |
| `fix:` | Bug fixes | `fix: correct ISO 8601 datetime parsing` |
| `refactor:` | Code restructuring | `refactor: simplify inheritance resolution logic` |
| `test:` | Test additions/changes | `test: add recurrence calculation edge cases` |
| `chore:` | Maintenance tasks | `chore: update dependencies` |

**Commit Message Structure:**

```
<type>: <description in imperative mood>

Optional body with details:
- What changed
- Why it changed
- Impact on existing functionality

Co-Authored-By: Claude <noreply@anthropic.com>
```

**Examples from AISD reference:**

- `docs: map checkout architecture and rate limiting baseline`
- `docs: plan checkout rate limiting (20 req/min)`
- `feat: add checkout rate limiting (20 req/min)`

**Key Rules:**

- Use lowercase for type prefix
- Imperative mood: "add" not "added"
- Concise description (50 chars preferred, 72 max)
- Reference issue numbers when applicable: `fix: correct date parsing (#42)`

---

## Project Overview

### What is TaskMark?

**Core Purpose:** Plain-text task management format that works everywhere

**Key Characteristics:**

- Markdown-based (`.md` files)
- Git-friendly version control
- Metadata inheritance through sections
- Powerful features (recurrence, subtasks, priorities)
- Simple by default, scales to complex projects

**Status:** Draft v1.1.0 (specification complete, no parser yet)

### Philosophy

| Principle | Implementation |
|-----------|----------------|
| Plain text is forever | UTF-8 Markdown, human-readable |
| Simple by default | `- [ ] Task` is valid, everything else optional |
| Powerful when needed | Priorities, dates, recurrence, inheritance |
| Git-native | Treat tasks like code (branch, merge, diff) |
| DRY principle | Metadata inheritance prevents repetition |

---

## Project Structure

```
taskmark-spec/
├── README.md                          # User-facing introduction
├── AGENTS.md                          # THIS FILE - AI agent guide
├── LICENSE                            # MIT license
├── docs/
│   ├── specification.md               # Complete technical spec
│   └── format-comparison.md           # Comparison with todo.txt, xit, etc.
└── examples/
    ├── sprint-planning.md             # Sprint with subtasks
    ├── team-standup.md                # Team coordination
    └── comprehensive.md               # Full feature showcase
```

---

## Core Syntax Reference

### Task States

| Symbol | State | Creates Recurrence | Use When |
|--------|-------|-------------------|----------|
| `[ ]` | Open | No | Task pending |
| `[x]` | Done | Yes (if `repeat:` present) | Task completed |
| `[-]` | Cancelled | No | Task abandoned/rejected |
| `[!]` | Blocked | No | Task paused/waiting |

**Case Insensitive:** `[X]` = `[x]`

### Complete Task Pattern

```markdown
- [STATE] (PRIORITY) [PLANNED_DATE] [DONE_DATE] Description @assignee +project #tag due:DATE repeat:PATTERN ~estimate key:value
  - [STATE] Subtask inherits parent metadata
```

**Component Order (Position-Based):**

| Position | Component | Required | Pattern |
|----------|-----------|----------|---------|
| 1 | State marker | YES | `- [ ]`, `- [x]`, `- [-]`, `- [!]` |
| 2 | Priority | No | `(A)`, `(1)`, `(urgent)`, `(P1)` |
| 3 | Planned date | No | `YYYY-MM-DD` or `YYYY-MM-DDTHH:MM` or `YYYY-MM-DDTHH:MM:SS±HH:MM` |
| 4 | Done date | No | Same format (only if planned date exists) |
| 5 | Description | YES | Plain text (1-unlimited chars) |
| 6+ | In-line metadata | No | Any order |

**In-line Metadata (Any Order):**

| Type | Pattern | Example | Notes |
|------|---------|---------|-------|
| Assignee | `@username` | `@alice @bob` | Case-insensitive, `a-zA-Z0-9_-` |
| Project | `+name[/sub]` | `+Acme/Backend`, `+app/v1.2.3` | Hierarchical `/`, versions with `.` |
| Tag | `#tag` | `#critical #backend` | Case-insensitive |
| Due date | `due:DATETIME` | `due:2024-03-15T18:00` | ISO 8601 format |
| Estimate | `~NUM[hmd]` | `~2h`, `~30m`, `~3d` | Hours, minutes, days |
| Recurrence | `repeat:PATTERN` | `repeat:weekdays`, `repeat:"every monday"` | Uses recurrent library |
| Custom metadata | `key:value` or `key:"value"` | `type:bug ticket:ENG-4701` | Keys case-insensitive |

### Date Formats

**Accepted:**

- Date only: `2024-03-10` (uses YAML front matter timezone or local)
- Date + time: `2024-03-10T09:00`
- Date + time + seconds: `2024-03-10T09:00:00`
- Date + time + timezone: `2024-03-10T09:00-05:00` (explicit timezone)

**Invalid Date Handling:** Warn at `file:line`, ignore field, continue parsing

### YAML Front Matter

**Supported Fields:**

| Field | Purpose | Example |
|-------|---------|---------|
| `locale` | Natural language date parsing + default output format | `en_US`, `fr_FR` |
| `timezone` | Default timezone for dates without explicit timezone | `America/New_York`, `UTC` |
| `date_format` | Output date format (strftime), overrides locale | `%Y-%m-%d`, `%d/%m/%Y` |

**File Scope Rules:**

- Front matter applies ONLY to the current file
- Linked files retain their own front matter
- No inheritance of front matter between files
- Default: system locale/timezone, ISO 8601 output (`YYYY-MM-DD`)

### Section Headers and Inheritance

```markdown
# TODO +GlobalProject #globalTag @team type:value

## Section Name +SectionProject #sectionTag @alice

- [ ] Task +TaskProject #taskTag @bob
```

**Inheritance Rules:**

| Metadata Type | Behavior | Example |
|---------------|----------|---------|
| Projects (`+`) | Hierarchical join with `/` | `+A` + `+B` + `+C` = `+A/B/C` |
| Tags (`#`) | Additive (all tags included) | `#api` + `#urgent` = both |
| Assignees (`@`) | Additive (all assignees included) | `@alice` + `@bob` = both |
| Key-value | Child overrides parent | Child `type:bug` overrides parent `type:feature` |

**Example:**

```markdown
# TODO +Acme #work @team

## Backend +API #critical @alice

- [ ] Task +Database @bob
```

**Task inherits:** `+Acme/API/Database`, `#work`, `#critical`, `@team`, `@alice`, `@bob`

### Subtasks

**Rules:**

- One level only (no sub-subtasks)
- Any indentation > 0 (spaces or tabs)
- Inherits ALL parent metadata + section metadata
- Independent state from parent
- Projects hierarchical join
- Tags additive
- Assignees additive

```markdown
- [ ] (A) Parent task @alice +Database #critical ~8h
  - [ ] (B) Update connection pooling @alice ~2h
  - [ ] (B) Add retry logic @bob ~3h
  - [x] (C) Write migration @alice ~3h
```

### File Links

**Syntax:** `[Link Text](path/to/file.md)` on standalone line

**Inheritance:** All tasks in linked file inherit section metadata where link appears

```markdown
# TODO +Acme #work

## Backend +Backend #critical

[API Team](backend/api.md)
[Database Team](backend/database.md)
```

**Result:** All tasks in both files inherit `+Acme/Backend`, `#work`, `#critical`

---

## Recurrence Behavior

### When Task Marked `[x]` with `repeat:`

**Old Task Changes:**

1. State → `[x]`
2. Add done date (2nd position after planned date)
3. Remove `repeat:` field

**New Task Created:**

1. State → `[ ]`
2. Dates updated to next occurrence
3. Keep `repeat:` field
4. Preserve all other metadata

### Date Calculation Algorithm

| Step | Action | Library |
|------|--------|---------|
| 1 | Parse `repeat:` pattern | recurrent |
| 2 | Extract RRULE + DTSTART | recurrent |
| 3 | Calculate next occurrence | python-dateutil.rrule |
| 4 | Calculate offset (if both planned & due) | pendulum |
| 5 | Apply offset to new dates | pendulum |

**Offset Preservation:**

```markdown
# Original
- [ ] Task planned:2024-03-10 due:2024-03-14 repeat:weekly

# After marking [x] on 2024-03-10
- [x] 2024-03-10 2024-03-10 Task due:2024-03-14
- [ ] Task planned:2024-03-17 due:2024-03-21 repeat:weekly
```

4-day offset between planned and due is preserved.

---

## Common Patterns and Conventions

### Priority Sorting

| Condition | Sort Method | Example |
|-----------|-------------|---------|
| All numeric | Numeric ascending | `(1)` < `(2)` < `(10)` |
| Any non-numeric | Alphanumeric ascending | `(A)` < `(A1)` < `(B)` |
| Same priority | Line number ascending | First in file comes first |

### Standard Metadata Keys (Convention, NOT Core Spec)

| Key | Common Values | Example |
|-----|---------------|---------|
| `type` | `bug`, `feature`, `docs`, `refactor`, `test` | `type:bug` |
| `epic` | Epic/initiative name | `epic:auth-v2` |
| `ticket` | Issue tracker ID | `ticket:ENG-4739` |
| `sprint` | Sprint number/name | `sprint:24` |
| `milestone` | Release version | `milestone:v2.0` |
| `url` | Full URL | `url:https://jira.example.com/ENG-4701` |
| `status` | Status name | `status:in-progress` |

### File Organization Strategies

**Single File (Small Projects):**

```markdown
# TODO +Project

## Feature Work
## Bug Fixes
## Documentation
```

**Multi-File (Large Projects):**

```markdown
# Main: project-todo.md
# TODO +Project #tag

## Engineering +Engineering
[Backend](team/backend.md)
[Frontend](team/frontend.md)

## Product +Product
[Features](team/features.md)
```

---

## Development Guidelines

### When Writing Parsers

**MUST Requirements:**

| Requirement | Rule |
|-------------|------|
| File encoding | UTF-8 only, fail on non-UTF-8 |
| Line endings | Accept both LF and CRLF |
| Date parsing | ISO 8601: `YYYY-MM-DD[THH:MM[:SS][±HH:MM]]` |
| Position-based dates | 1st datetime = planned, 2nd = done |
| Recurrence parsing | Use recurrent library |
| Recurrence calculation | Use python-dateutil.rrule |
| Case matching | Treat case-insensitive: `@User` = `@user` |
| State recognition | Only `[ ]`, `[x]`, `[-]`, `[!]` |
| Subtask depth | Reject > 1 level (warn + treat as 1-level) |

**Error Handling:**

| Error Type | Action | Log Level |
|------------|--------|-----------|
| Malformed date | Warn at `file:line`, ignore field | WARNING |
| Multiple priorities | Use first, ignore rest | INFO |
| Invalid task state | Ignore line | INFO |
| Unclosed quote | Warn, treat as unquoted | WARNING |
| Sub-subtask | Warn, treat as subtask | WARNING |

### When Writing Documentation

**Tone:**

- Clear, concise, scannable
- Tables over prose when possible
- Explicit constraints (not vague)
- Use MUST/REQUIRED/SHOULD/MAY from RFC 2119

**Examples:**

- Use realistic software project scenarios
- Show minimal → full-featured progression
- Include "after inheritance" results

### When Writing Examples

**Required Elements:**

1. YAML front matter with locale/timezone (if using times)
2. Top-level `# TODO` header
3. Section headers for organization
4. Mix of task states (`[ ]`, `[x]`, `[-]`, `[!]`)
5. Representative metadata usage
6. Comments explaining inheritance

---

## Common Pitfalls and Solutions

### Pitfall: Forgetting Inheritance

**Problem:**

```markdown
# TODO +Acme

## Backend
- [ ] Task +Acme   # WRONG: Redundant project
```

**Solution:**

```markdown
# TODO +Acme

## Backend +Backend
- [ ] Task   # Inherits +Acme/Backend
```

### Pitfall: Deep Subtask Nesting

**Problem:**

```markdown
- [ ] Task
  - [ ] Subtask
    - [ ] Sub-subtask   # INVALID
```

**Solution:**

```markdown
- [ ] Task
  - [ ] Subtask 1
  - [ ] Subtask 2

- [ ] Sub-subtask as separate task
```

### Pitfall: Inline File Links

**Problem:**

```markdown
- [ ] Check [tasks](team.md) for details   # Link ignored
```

**Solution:**

```markdown
## Section

[Team Tasks](team.md)

- [ ] Check tasks for details
```

### Pitfall: Ambiguous Dates

**Problem:**

```markdown
- [ ] Task due:03-10   # Ambiguous format
```

**Solution:**

```markdown
- [ ] Task due:2024-03-10   # ISO 8601 required
```

### Pitfall: Missing Quotes in Values

**Problem:**

```markdown
- [ ] Task notes:This is a long note   # Stops at first space
# Parsed as: notes:This
```

**Solution:**

```markdown
- [ ] Task notes:"This is a long note"
```

---

## AI Agent Workflow

### When Asked to Create Todo Files

1. **Start Simple:** `# TODO` header + basic tasks
2. **Add Structure:** Sections with descriptive names
3. **Apply Metadata:** Projects, tags on sections (not tasks)
4. **Use Inheritance:** Let tasks inherit from sections
5. **Add Details:** Priorities, dates, estimates only where needed

### When Asked to Update Tasks

1. **Read First:** Never edit without reading current state
2. **Preserve Format:** Maintain existing indentation, metadata style
3. **Check Recurrence:** If `repeat:` present, understand implications
4. **Update Inheritance:** Consider section metadata changes

### When Asked About Format

1. **Cite Spec:** Reference [specification.md](docs/specification.md)
2. **Show Examples:** Use realistic project scenarios
3. **Explain Trade-offs:** Compare with todo.txt, xit, etc.
4. **Link Comparison:** Reference [format-comparison.md](docs/format-comparison.md)

### When Implementing Features

1. **Check Spec First:** [specification.md](docs/specification.md) is source of truth
2. **Follow Library Stack:** dateparser, recurrent, python-dateutil, pendulum
3. **Error Handling:** Warn at `file:line`, continue parsing
4. **Test Edge Cases:** Invalid dates, deep nesting, malformed syntax

---

## Testing Scenarios

### Valid Inputs to Support

```markdown
# Minimal valid
- [ ] Task

# Full featured
- [ ] (A) 2024-03-10T09:00-05:00 Task @user +proj/sub #tag1 #tag2 due:2024-03-15 repeat:weekdays ~2h type:bug

# Subtasks with inheritance
## Section +Proj #tag
- [ ] Parent @alice
  - [ ] Child inherits @alice +Proj #tag

# File links
## Engineering +Eng
[Team](team.md)  # All tasks in team.md inherit +Eng

# Recurrence
- [ ] 2024-03-18T09:00 Standup repeat:"every monday at 9am"
```

### Invalid Inputs to Handle Gracefully

```markdown
# Malformed date (warn + skip)
- [ ] Task due:2024-13-99

# Deep nesting (warn + flatten)
- [ ] Task
  - [ ] Subtask
    - [ ] Sub-subtask   # Treat as subtask

# Invalid state (ignore line)
- [?] Unknown state

# Unclosed quote (warn + treat as unquoted)
- [ ] Task notes:"unclosed
```

---

## Quick Reference

### Minimal Task

```markdown
- [ ] Task description
```

### Everything

```markdown
- [ ] (A) 2024-03-10T09:00-05:00 Task @user +project/sub #tag created:2024-03-01T10:00 started:2024-03-10T09:00 due:2024-03-15T18:00 repeat:weekdays ~2h type:bug ticket:ENG-4739
  - [ ] Subtask
```

### Section with Inheritance

```markdown
# TODO +global-project #global-tag @team

## Section Name +section-project #section-tag @alice

- [ ] Task inherits: +global-project/section-project, #global-tag, #section-tag, @team, @alice
```

### File Link with Inheritance

```markdown
## Backend +Acme #critical @alice

[API Team](team/api.md)

# All tasks in api.md inherit: +Acme, #critical, @alice
```

---

## Resources

| Document | Purpose | When to Use |
|----------|---------|-------------|
| [README.md](README.md) | User introduction | Explaining format to humans |
| [specification.md](docs/specification.md) | Complete technical spec | Implementation questions |
| [format-comparison.md](docs/format-comparison.md) | Format comparison | Understanding design decisions |
| [sprint-planning.md](examples/sprint-planning.md) | Sprint example | Learning basics |
| [team-standup.md](examples/team-standup.md) | Team coordination | Intermediate example |
| [comprehensive.md](examples/comprehensive.md) | Full feature showcase | Advanced usage |

---

## Implementation Status

| Component | Status | Notes |
|-----------|--------|-------|
| Specification | ✅ Complete | Draft v1.1.0 |
| Reference Parser | ⏳ Not started | Python implementation planned |
| VSCode Extension | ⏳ Not started | Syntax highlighting, task detection |
| CLI Tool | ⏳ Not started | Query, filter, update tasks |
| Web Viewer | ⏳ Not started | Read-only task viewer |

---

## Philosophy for AI Agents

### AISD Principles Applied

This document follows AISD (AI Small Docs) principles:

1. **Tables over prose** — Scannable structures for quick lookup
2. **Explicit constraints** — "MUST be UTF-8" not "should use UTF-8"
3. **Small focused content** — Each section addresses one concern
4. **AI-authored, human-reviewed** — Generated by Claude, reviewed by humans

### Working with This Project

**When in doubt:**

1. Read the spec: [specification.md](docs/specification.md)
2. Check examples: [examples/](examples/)
3. Compare formats: [format-comparison.md](docs/format-comparison.md)
4. Ask questions: Clarify before implementing

**Core values:**

- Plain text simplicity
- Git-native workflow
- Human readability
- Machine parseability
- Future-proof format
