# Todo Format Comparison

This document compares existing plain-text todo formats that inspired the TaskMark specification.

---

## Format Overview

| Format | Repository | Author(s) | Year | Philosophy |
|--------|-----------|-----------|------|------------|
| **todo.txt** | [todotxt/todo.txt](https://github.com/todotxt/todo.txt) | Gina Trapani | 2006 | Future-proof, plain text simplicity |
| **todo.md** | [todomd/todo.md](https://github.com/todomd/todo.md) | Janik von Rotz, Lars Quentin | 2020 | Markdown-based, Git-friendly |
| **xit** | [jotaen/xit](https://github.com/jotaen/xit) | jotaen | 2021 | Minimalist journal format |
| **.todo** | [matthewpalmer/.todo](https://github.com/matthewpalmer/.todo) | Matthew Palmer | 2015 | Cross-platform, Markdown-like |

---

## Core Feature Comparison

| Feature | todo.txt | todo.md | xit | .todo | TaskMark |
|---------|----------|---------|-----|-------|-----------|
| **Task States** | `x` (done) | `[ ]`, `[x]`, `[-]` | `[ ]`, `[x]`, `[-]` | `[ ]`, `[x]` | `[ ]`, `[x]`, `[-]`, `[!]` |
| **Priority** | `(A-Z)` | No standard | No | Variable | `(any)` |
| **Assignees** | No | `@user` | No | Variable | `@user` |
| **Projects** | `+project` | No standard | `project:name` | Variable | `+project` with `/` hierarchy |
| **Contexts/Tags** | `@context` | `#tag` | No | Variable | `#tag` |
| **Due Dates** | `due:YYYY-MM-DD` | `due:YYYY-MM-DD` | `key:value` | Variable | `due:YYYY-MM-DD` (core) |
| **Estimates** | `est:Xh` | `~Xh` | Variable | Variable | `~Xh/m/d` |
| **Recurrence** | No | No | No | No | `repeat:*` (core) |
| **Subtasks** | No (flat list) | Yes (indented) | Yes (indented) | Yes (indented) | Yes (one level) |
| **Sections** | No | Yes (headers) | Yes (dates) | Yes (headers) | Yes (headers) |
| **File Links** | No | No | No | No | `[text](file.md)` (core) |
| **Metadata** | `key:value` | Inline text | `key:value` | Separate lines | `key:value` |

---

## Syntax Comparison

### Task Format

| Format | Example |
|--------|---------|
| **todo.txt** | `(A) 2025-11-19 Fix bug +Project @context due:2025-12-01` |
| **todo.md** | `- [ ] Fix bug @alice #urgent due:2025-12-01 ~2h` |
| **xit** | `[ ] Fix bug project:App context:backend due:2025-12-01` |
| **.todo** | `- [ ] Fix bug` (metadata on separate lines) |
| **TaskMark** | `- [ ] (A) Fix bug @alice +Project #urgent due:2025-12-01 ~2h` |

### Subtasks

| Format | Support | Syntax |
|--------|---------|--------|
| **todo.txt** | No | Workaround: separate tasks with naming convention |
| **todo.md** | Yes | Indented `- [ ]` under parent (2+ spaces) |
| **xit** | Yes | Indented `[ ]` under parent |
| **.todo** | Yes | Indented `- [ ]` under parent |
| **TaskMark** | Yes | Indented `- [ ]` under parent (any spaces/tabs, one level) |

### Sections

| Format | Support | Syntax |
|--------|---------|--------|
| **todo.txt** | No | Projects serve as implicit sections |
| **todo.md** | Yes | `# TODO`, `## Section`, `### Subsection` |
| **xit** | Yes | Date headers: `2025-11-19` |
| **.todo** | Yes | Markdown headers |
| **TaskMark** | Yes | `# TODO`, `## Section` with metadata inheritance |

---

## Key Differences from Existing Formats

### vs todo.txt

| Aspect | todo.txt | TaskMark | Rationale |
|--------|----------|-----------|-----------|
| Contexts vs Assignees | `@context` for location/tool | `@user` for people, `#tag` for contexts | Clearer separation of who vs where |
| Structure | Flat list only | Hierarchical sections + subtasks | Better organization for complex projects |
| Dates | Creation date REQUIRED | Optional dates | Less rigid, more flexible |
| Metadata | Extensions only | Core dates + recurrence | Built-in time management |

### vs todo.md (original)

| Aspect | todo.md | TaskMark | Rationale |
|--------|---------|-----------|-----------|
| Task states | `[ ]`, `[x]`, `[-]` | `[ ]`, `[x]`, `[-]`, `[!]` | Added blocked state |
| Priority | Not standardized | `(value)` after state | Consistent with todo.txt |
| Metadata | No standard | `key:value` + core fields | Machine-parseable structure |
| Recurrence | No | `repeat:*` fields | Time-based task automation |
| File links | No | `[text](file.md)` | Multi-file organization |
| Inheritance | No | Section → task → subtask | DRY principle for metadata |

### vs xit

| Aspect | xit | TaskMark | Rationale |
|--------|-----|-----------|-----------|
| Organization | Date-based journal | Section-based projects | Better for non-temporal tasks |
| Priority | No | `(value)` | Explicit importance ranking |
| Projects/Tags | Metadata only | First-class `+proj` `#tag` | Faster filtering/querying |
| Recurrence | No | Built-in | Repeating tasks common use case |

---

## Example: Same Task Set Across Formats

**Scenario:** Warp drive repair with 3 subtasks, assigned to Geordi, high priority, due 2366-03-15

### todo.txt
```
(A) 2366-03-01 Repair warp drive containment field +Enterprise @engineering due:2366-03-15 est:8h
(B) 2366-03-01 Replace magnetic constrictors +Enterprise @engineering due:2366-03-12 est:2h parent:warp-drive-repair
(B) 2366-03-01 Recalibrate plasma injectors +Enterprise @engineering due:2366-03-13 est:3h parent:warp-drive-repair
(B) 2366-03-01 Test at warp 5 +Enterprise @engineering due:2366-03-14 est:3h parent:warp-drive-repair
```

### todo.md (original)
```markdown
## Engineering

- [ ] Repair warp drive containment field @geordi #critical due:2366-03-15 ~8h
  - [ ] Replace magnetic constrictors ~2h
  - [ ] Recalibrate plasma injectors ~3h
  - [ ] Test at warp 5 ~3h
```

### xit
```
2366-03-01
    [ ] Repair warp drive containment field assignee:geordi project:Enterprise context:engineering due:2366-03-15 est:8h
        [ ] Replace magnetic constrictors est:2h
        [ ] Recalibrate plasma injectors est:3h
        [ ] Test at warp 5 est:3h
```

### TaskMark
```markdown
## Engineering Operations +Enterprise #engineering

- [ ] (A) Repair warp drive containment field @geordi +WarpCore #critical due:2366-03-15 ~8h type:urgent
  - [ ] (B) Replace magnetic constrictors @geordi ~2h
  - [ ] (B) Recalibrate plasma injectors @barclay ~3h
  - [x] (C) 2366-03-09 2366-03-10 Test at warp 5 @geordi ~3h
```

**After inheritance:**
- Parent: `+Enterprise/WarpCore`, `#engineering`, `#critical`, `type:urgent`
- Subtasks inherit all parent metadata + section metadata

---

## Design Decisions

### Why `@user` for people (not `@context`)?

| Reason | Details |
|--------|---------|
| Social media familiarity | `@mention` universally understood as person reference |
| Clear semantics | Unambiguous: `@` = who, `#` = what/where |
| Distinct from contexts | Allows both: `@geordi` (who) + `#engineering` (where) |

### Why `+project` (from todo.txt)?

| Reason | Details |
|--------|---------|
| Established convention | Widely recognized from todo.txt ecosystem |
| Natural grouping | Plus sign implies addition/aggregation |
| Hierarchy support | `/` separator for sub-projects: `+Enterprise/WarpCore` |

### Why `#tag` for contexts (not `@context`)?

| Reason | Details |
|--------|---------|
| Hashtag metaphor | Familiar categorization pattern |
| GTD alignment | Tags represent where/how work happens |
| Multiple contexts | Tasks can have multiple tags: `#engineering #critical` |

### Why sections with inheritance?

| Reason | Details |
|--------|---------|
| DRY principle | Don't repeat project/tag on every task |
| Visual organization | Markdown headers provide clear structure |
| Git-friendly | Sections create logical merge boundaries |
| Scalability | Large projects split into manageable files |

### Why recurrence fields?

| Reason | Details |
|--------|---------|
| Common use case | Many tasks repeat (standups, reports, reviews) |
| Automation-friendly | Tools can auto-create next instance |
| RRULE inspiration | Proven pattern from iCalendar spec |

### Why file links?

| Reason | Details |
|--------|---------|
| Multi-file projects | Large teams need task distribution |
| Modular organization | Separate concerns into different files |
| Inheritance benefits | Link inherits section metadata |
| Markdown standard | `[text](file.md)` standard Markdown syntax |

---

## Synthesis: Best of All Formats

| Feature | Source | Improvement |
|---------|--------|-------------|
| Plain text simplicity | todo.txt | UTF-8 Markdown for readability |
| Task states `[x]` | xit, todo.md | Added `[!]` for blocked |
| Priority `(A)` | todo.txt | Flexible: letters, numbers, symbols |
| Assignees `@user` | todo.md | Case-insensitive matching |
| Projects `+proj` | todo.txt | Hierarchical with `/` |
| Tags `#tag` | todo.md | Additive inheritance |
| Sections | todo.md | With metadata inheritance |
| Subtasks | xit, todo.md | One level, clear rules |
| Dates `YYYY-MM-DD` | todo.txt | Core spec, not extension |
| Recurrence | New | RRULE-inspired, auto-repeat |
| File links | New | Markdown links with inheritance |
| Metadata `key:value` | todo.txt, xit | Core + convention split |

---

## Migration Paths

### From todo.txt

| Element | todo.txt | TaskMark | Notes |
|---------|----------|-----------|-------|
| Tasks | One per line | `- [ ] Task` | Add checkbox syntax |
| Priority | `(A)` at start | `(A)` after state | Move after `- [ ]` |
| Contexts | `@context` | `#tag` | Swap symbol |
| Projects | `+project` | `+project` | Compatible |
| Assignees | None | `@user` | Add if needed |
| Sections | None | `## Section` | Group by project |
| Dates | Creation date | Optional | Remove if not needed |

### From todo.md (original)

| Element | todo.md | TaskMark | Notes |
|---------|---------|-----------|-------|
| Task states | `[ ]`, `[x]`, `[-]` | `[ ]`, `[x]`, `[-]`, `[!]` | Add blocked if needed |
| Priority | None | `(value)` after state | Add if needed |
| Metadata | Freeform | `key:value` | Standardize format |
| Recurrence | None | `repeat:*` | Add if needed |
| File links | None | `[text](file.md)` | Add for multi-file |

### From xit

| Element | xit | TaskMark | Notes |
|---------|-----|-----------|-------|
| Date headers | `2025-11-19` | `## Section` or `due:` | Reorganize by project |
| Task states | `[ ]`, `[x]`, `[-]` | `[ ]`, `[x]`, `[-]`, `[!]` | Compatible + blocked |
| Metadata | `key:value` | `key:value` + core | Extract core fields |
| Priority | None | `(value)` | Add if needed |
| Projects | `project:name` | `+name` | Convert syntax |

---

**End of Comparison**
