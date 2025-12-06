# Examples

Format examples. See [specification.md](specification.md).

---

## Minimal

```markdown
# Tasks
- [ ] Task
```

---

## States

```markdown
- [ ] Open task
- [.] In progress @alice
- [x] Done done:2024-03-10
- [-] Cancelled
- [!] Blocked
```

---

## Full Task

```markdown
- [ ] (A) Task +Project @alice #critical ~8h planned:2024-03-10 due:2024-03-15 type:feature
  - [ ] (B) Subtask @bob ~2h
  - [ ] Subtask 2 ~3h
  - Note for context
  - Another note #repeat
```

---

## Inheritance

```markdown
# Project +Acme #work @team

## Backend +API #critical @alice

- [ ] Task
# Inherits: +Acme/API, #work #critical, @team @alice
```

---

## Recurring

```markdown
- [ ] Weekly review repeat:weekly
  - [ ] Check metrics #repeat
  - Personal notes

# After completion:
- [ ] Weekly review repeat:weekly planned:2024-03-22
  - [ ] Check metrics #repeat
- [x] Weekly review done:2024-03-15T14:30
  - [ ] Check metrics #repeat
  - Personal notes
```

---

## Dates

```markdown
- [ ] Task planned:2024-03-10
- [ ] Meeting planned:2024-03-15T09:00 due:2024-03-15T09:30
- [ ] Call planned:2024-03-15T09:00-05:00
```

---

## Frontmatter

```markdown
---
timezone: America/New_York
datetime_format: "%d/%m/%Y[ %H:%M]"
---

# Tasks
- [ ] Task planned:15/03/2024
```

---

## Multi-File

**main.md:**

```markdown
# Project +Project

## Engineering
[Backend](team/backend.md)
```

**team/backend.md:**

```markdown
# Backend Tasks
- [ ] API design @alice
```

Backend inherits `+Project` from parent section.

---

## Custom Metadata

```markdown
- [ ] Task type:bug priority:high ticket:ENG-123 sprint:24
```

---

## Notes

```markdown
- [ ] Task
  - [ ] Subtask
  - Note text
    continuation line
  - Another note #repeat
```
