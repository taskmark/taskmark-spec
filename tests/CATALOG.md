# Golden File Test Catalog

Conformance tests for TaskMark parsers and mutators.

---

## Test Structure

```
tests/
├── CATALOG.md                  # This file
├── TESTING.md                  # How golden tests work
└── T{NN}_{name}/
    ├── input.md                # Root file to parse
    ├── input_{linked}.md       # Optional linked files
    ├── parsed.yaml             # Expected parse output
    ├── mutation.yaml           # Mutations to apply
    ├── mutated.md              # Expected output after mutation
    └── mutated_{linked}.md     # Optional mutated linked files
```

Each test is a complete multi-stage flow: **parse → verify → mutate → serialize → verify**.

---

## Quick Reference

| Test | Purpose |
|------|---------|
| T01_minimal | Simplest valid file |
| T02_all_states | All 5 states + transitions |
| T03_metadata_full | All metadata types |
| T04_inheritance | Section hierarchy |
| T05_subtasks_notes | Subtasks and notes |
| T06_frontmatter | Multi-file with locales/timezones |
| T07_recurrence | Recurring tasks |
| T08_multi_file | File links + cross-file inheritance |
| T09_escaping | Escape sequences |
| T10_edge_cases | Parser resilience |
| T11_locales | 8 language date formats |
| T12_team_standup | Real-world team standup example |
| T13_sprint_planning | Sprint planning with recurrence |
| T14_custom_date_format | UK locale with custom datetime format |
| T15_comprehensive | Full feature coverage example |

---

## Detailed Coverage

### T01: Minimal

**Purpose:** Absolute minimum valid TaskMark file.

| Feature | Tested | Details |
|---------|--------|---------|
| Parse single task | ✅ | No metadata |
| Markdown header | ✅ | `# Tasks` section |
| State `[ ]` open | ✅ | Default state |
| Mutation: state change | ✅ | `open` → `done` |
| Auto-add `done:` date | ✅ | On completion |

---

### T02: All States

**Purpose:** All 5 task states with transitions.

| Feature | Tested | Details |
|---------|--------|---------|
| State `[ ]` open | ✅ | |
| State `[.]` in_progress | ✅ | With `started:` |
| State `[x]` done | ✅ | Lowercase |
| State `[X]` done | ✅ | Uppercase (case insensitive) |
| State `[-]` cancelled | ✅ | |
| State `[!]` blocked | ✅ | With `paused:` |
| Transition: open → in_progress | ✅ | Adds `started:` |
| Transition: in_progress → blocked | ✅ | Adds `paused:`, keeps `started:` |
| Transition: blocked → done | ✅ | Adds `done:`, keeps `paused:` |
| Transition: cancelled → open | ✅ | Reopens |
| Transition: done → open | ✅ | Clears `done:` |
| Transition: in_progress → open | ✅ | Clears `started:` |
| Transition: blocked → open | ✅ | Clears `paused:` |

---

### T03: Metadata Full

**Purpose:** All metadata types on tasks.

| Feature | Tested | Details |
|---------|--------|---------|
| Priority `(A)` letter | ✅ | |
| Priority `(1)` numeric | ✅ | |
| Project `+Name` | ✅ | |
| Project hierarchy `+A/B` | ✅ | |
| Assignee `@name` | ✅ | Multiple |
| Tag `#name` | ✅ | Multiple |
| Estimate `~Nh` hours | ✅ | |
| Estimate `~Nm` minutes | ✅ | |
| Estimate `~Nd` days | ✅ | |
| Date `created:` | ✅ | Parse only |
| Date `planned:` | ✅ | |
| Date `started:` | ✅ | |
| Date `paused:` | ✅ | |
| Date `due:` | ✅ | |
| Date `done:` | ✅ | |
| Custom field `key:value` | ✅ | |
| Custom field `key:"quoted"` | ✅ | |
| Mutation: change priority | ✅ | |
| Mutation: change assignees | ✅ | Add/remove |
| Mutation: change tags | ✅ | Add/remove |
| Mutation: change estimate | ✅ | |
| Mutation: change custom fields | ✅ | Add/modify |
| Mutation: remove all assignees | ✅ | Set to `[]` |
| Mutation: remove all tags | ✅ | Set to `[]` |
| Mutation: change project | ✅ | |
| Mutation: change dates | ❌ | NOT TESTED (only auto-set) |

---

### T04: Inheritance

**Purpose:** Metadata inheritance through section hierarchy.

| Feature | Tested | Details |
|---------|--------|---------|
| Header `#` level 1 | ✅ | |
| Header `##` level 2 | ✅ | |
| Header `###` level 3 | ✅ | |
| Project hierarchical join | ✅ | `+A` + `+B` = `A/B` |
| Tag additive | ✅ | Parent + child merged |
| Tag deduplication | ✅ | No duplicates |
| Assignee additive | ✅ | Parent + child merged |
| Assignee deduplication | ✅ | No duplicates |
| Custom field override | ✅ | Child replaces parent |
| Explicit vs inherited tracking | ✅ | Separate fields |
| Priority inheritance | ❌ | NOT TESTED |
| Estimate inheritance | ❌ | NOT TESTED |

---

### T05: Subtasks and Notes

**Purpose:** Subtask and note parsing rules.

| Feature | Tested | Details |
|---------|--------|---------|
| Subtask detection | ✅ | Indented `- [STATE]` |
| Subtask all 5 states | ✅ | |
| Note detection | ✅ | Indented `- text` (no brackets) |
| Invalid state as note | ✅ | `[invalid]` → note |
| Subtask @assignee → parent | ✅ | Propagates up |
| Subtask #tag → parent | ✅ | Propagates up |
| `#repeat` on subtask | ✅ | NOT propagated to parent |
| `#repeat` on note | ✅ | `has_repeat_tag: true` |
| Subtask completion | ✅ | Sets `done:` |
| Parent completion | ✅ | Doesn't auto-complete subtasks |
| Subtask priority → parent | ❌ | NOT TESTED |
| Multi-level subtasks | ✅ | Subtask of subtask |

---

### T06: Frontmatter (Multi-File)

**Purpose:** Multi-file frontmatter with different locales and timezones.

| Feature | Tested | Details |
|---------|--------|---------|
| Frontmatter parsing | ✅ | YAML in `---` |
| `locale` setting | ✅ | en_US, en_GB, ja_JP |
| `timezone` setting | ✅ | America/New_York, Europe/London, Asia/Tokyo |
| `date_format` setting | ✅ | Various strftime formats |
| No frontmatter (ISO default) | ✅ | Root file |
| Locale-specific date parsing | ✅ | |
| Locale-specific date serialization | ✅ | |
| UTC date conversion | ✅ | `Z` suffix |
| Explicit TZ date conversion | ✅ | `-05:00` suffix |
| Time component in dates | ✅ | Optional `[ %H:%M]` |

---

### T07: Recurrence

**Purpose:** Recurring task behavior on state changes.

| Feature | Tested | Details |
|---------|--------|---------|
| `repeat:daily` | ✅ | Parse only |
| `repeat:weekly` | ✅ | Full test |
| `repeat:monthly` | ✅ | Advances by 1 month |
| `repeat:yearly` | ✅ | Advances by 1 year |
| `repeat:every-N-days` | ❌ | NOT TESTED |
| Completion creates new task | ✅ | Above completed |
| Completion removes `repeat:` | ✅ | From completed task |
| `#repeat` subtasks copied | ✅ | |
| `#repeat` notes copied | ✅ | |
| Non-`#repeat` stays with completed | ✅ | |
| Date advancement | ✅ | `planned:`, `due:` |
| Cancel removes `repeat:` | ✅ | No new task |
| Block keeps `repeat:` | ✅ | No new task, adds `paused:` |

---

### T08: Multi-File

**Purpose:** File link parsing and cross-file inheritance.

| Feature | Tested | Details |
|---------|--------|---------|
| File link `[[file.md]]` | ✅ | |
| Linked file inheritance | ✅ | From link location section |
| Linked file own frontmatter | ✅ | |
| Cross-file mutation | ✅ | Saves to correct file |
| Cross-file date format | ✅ | Each file uses own |
| Broken file link | ✅ | Error E005 |
| Circular file links | ❌ | NOT TESTED |

---

### T09: Escaping

**Purpose:** All escape sequences.

| Feature | Tested | Details |
|---------|--------|---------|
| `\@` literal @ | ✅ | Not assignee |
| `\+` literal + | ✅ | Not project |
| `\#` literal # | ✅ | Not tag |
| `\~` literal ~ | ✅ | Not estimate |
| `\:` literal : | ✅ | Not key:value |
| Mixed escaped + real | ✅ | `+Project \@literal` |
| Unicode preserved | ✅ | café, résumé |
| Emoji preserved | ✅ | 🎯 📝 ✅ |
| HTML entities preserved | ✅ | `<>&"'` |
| Backticks preserved | ✅ | `` `code` `` |
| Escape round-trip | ✅ | Parse → serialize |
| Double backslash `\\` | ✅ | `\\+` = literal `\+` |

---

### T10: Edge Cases

**Purpose:** Parser resilience and boundary conditions.

| Feature | Tested | Details |
|---------|--------|---------|
| Extra whitespace in title | ✅ | Trimmed |
| Tab in title | ✅ | Preserved |
| Empty title `- [ ]` | ⚠️ | Undefined behavior |
| Single char title | ✅ | `A`, `1` |
| Malformed `- []` | ✅ | No space → preserved |
| Malformed `- [y]` | ✅ | Invalid state → preserved |
| Malformed `- [  ]` | ✅ | Double space → preserved |
| Orphan subtask | ✅ | Becomes top-level |
| Duplicate project | ✅ | Last wins |
| Duplicate date | ✅ | Last wins |
| Duplicate assignee | ✅ | Deduplicated |
| Duplicate tag | ✅ | Deduplicated |
| Deep nesting (5 levels) | ✅ | |
| Deep nesting (10+ levels) | ❌ | NOT TESTED |
| Long content | ✅ | 200+ chars |
| Mixed indentation | ✅ | Tabs + spaces |
| Error: task not found | ✅ | Mutation error |
| Error: ambiguous target | ✅ | Multiple tasks same title |
| Error: invalid transition | ❌ | NOT TESTED |

---

### T11: Locales (Multi-Language)

**Purpose:** Locale-specific date parsing across 8 languages.

| Feature | Tested | Details |
|---------|--------|---------|
| English (US) | ✅ | `March 15, 2024` |
| English (UK) | ✅ | `15 March 2024` |
| Spanish | ✅ | `15 de marzo de 2024` |
| German | ✅ | `15. März 2024` |
| Portuguese (BR) | ✅ | `15 de março de 2024` |
| Dutch | ✅ | `15 maart 2024` |
| Russian | ✅ | `15 марта 2024` |
| Chinese | ✅ | `2024年03月15日` |
| French | ❌ | NOT TESTED |
| Italian | ❌ | NOT TESTED |
| Japanese (kanji months) | ❌ | NOT TESTED |
| Timezone conversion | ✅ | All files |
| Date+time serialization | ✅ | Optional time |

---

### T12: Team Standup (Example)

**Purpose:** Real-world team standup scenario.

| Feature | Tested | Details |
|---------|--------|---------|
| Section inheritance | ✅ | @backend-team, @frontend-team, @ops |
| Project inheritance | ✅ | +work from header |
| Tag inheritance | ✅ | #standup from header |
| All 5 task states | ✅ | Real-world usage |
| Subtask completion | ✅ | Complete blocked subtask |
| Priority letters | ✅ | (A), (B) |
| Multiple assignees | ✅ | Section + explicit |
| Estimate formats | ✅ | ~4h, ~8h, ~16h |

---

### T13: Sprint Planning (Example)

**Purpose:** Sprint planning with recurrence scenario.

| Feature | Tested | Details |
|---------|--------|---------|
| Recurrence completion | ✅ | Weekly task spawns new |
| Custom metadata | ✅ | desc:"quoted value" |
| Nested subtasks | ✅ | 3-level deep |
| Project hierarchy | ✅ | +engineering/backend |
| Tag combinations | ✅ | #q4, #security, #bugfix |
| Multiple sections | ✅ | Backend, Frontend, Documentation |

---

### T14: Custom Date Format (Example)

**Purpose:** UK locale with frontmatter datetime_format scenario.

| Feature | Tested | Details |
|---------|--------|---------|
| Frontmatter locale | ✅ | en_GB |
| Frontmatter datetime_format | ✅ | %d/%m/%Y[ %H:%M] |
| Frontmatter timezone | ✅ | Europe/London |
| DD/MM/YYYY parsing | ✅ | UK date format |
| Time component | ✅ | Optional HH:MM |
| State: in_progress | ✅ | Subtask started |
| Notes with #repeat | ✅ | Preserved correctly |

---

### T15: Comprehensive (Example)

**Purpose:** Full feature coverage scenario.

| Feature | Tested | Details |
|---------|--------|---------|
| Deep section nesting | ✅ | ### level 3 headers |
| Custom metadata | ✅ | status:active, env:production |
| Escaped characters | ✅ | \@mention, \#hashtag |
| Multiple projects | ✅ | +alpha/sprint1/api |
| Long titles | ✅ | 80+ character title |
| Minute estimates | ✅ | ~15m, ~30m |
| Day estimates | ✅ | ~2d |
| Multiple assignees | ✅ | @user1 @user2 @user3 |
| Multiple tags | ✅ | #tag1 #tag2 #tag3 |
| Priority override | ✅ | Explicit vs inherited |
| Project override | ✅ | +explicit-project |
| Standalone sections | ✅ | No project inheritance |

---

## Coverage Gaps Summary

### Remaining Gaps

| Gap | Severity | Recommendation |
|-----|----------|----------------|
| `repeat:every-N-days` | MEDIUM | Add to T07 |
| Circular file links | MEDIUM | Add to T08 |
| Deep nesting 10+ levels | LOW | Add to T10 |
| Error: invalid transition | LOW | Add to T10 |
| More locales (FR, IT, JA) | LOW | Add to T11 |
| Priority/estimate inheritance | LOW | Add to T04 |
| Subtask priority → parent | LOW | Add to T05 |
| Empty title behavior | LOW | Define spec first |
| Mutation: change dates | LOW | Add to T03 |

### Recently Added Coverage

- ✅ T02: Reopen transitions (done→open, in_progress→open, blocked→open)
- ✅ T03: Remove all assignees/tags, change project
- ✅ T05: Multi-level subtasks (subtask of subtask)
- ✅ T07: Monthly and yearly recurrence patterns
- ✅ T08: Broken file link error
- ✅ T09: Double backslash escape
- ✅ T10: Ambiguous target error
- ✅ T12-T15: Real-world scenario tests (team standup, sprint planning, custom date format, comprehensive)

---

## Serialization Rules

### Metadata Ordering

- **Tags**: Serialized in alphabetical order
- **Assignees**: Serialized in alphabetical order
- **Custom fields**: Serialized in alphabetical order by key

### Value Quoting

Values containing spaces must be quoted:

- `planned:"March 15, 2024 09:00"` (has space)
- `planned:2024-03-15` (no space, no quotes needed)
- `ticket:"ENG-123"` (contains special characters)

### Indentation

- 2 spaces = 1 indent level
- 1 tab = 1 indent level
- Mixed indentation is supported but not recommended

---

## Special Tag Behavior

### #repeat Tag

The `#repeat` tag has special handling:

- On subtasks: NOT propagated to parent (unlike other tags)
- On notes: Marks note for copying to new recurring instance
- Purpose: Control what gets copied when recurring task completes

---

## Date Field Preservation

- `paused:` is preserved when a blocked task is completed
- `started:` is preserved through state transitions
- Only `done:` is cleared when reopening a done task

---

## Implementation Notes

1. **Test isolation:** Each test is independent; no shared state
2. **Deterministic output:** All mutations use fixed `today` date
3. **Multi-stage:** Each test covers parse + mutate + serialize
4. **Inheritance tracking:** Distinguish explicit vs inherited metadata
5. **File identity:** Track which file each task belongs to
6. **Idempotency:** Parse → serialize → parse produces identical result
