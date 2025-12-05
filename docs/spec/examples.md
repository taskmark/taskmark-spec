# TaskMark Examples

Detailed examples for the TaskMark format. See [specification.md](../specification.md) for the core spec.

---

## Minimal

```markdown
# TODO
- [ ] Task
```

---

## Full Featured

```markdown
# TODO +Acme #work

## Backend Operations +Backend #critical

- [ ] (A) 2024-03-10 Fix database connection @alice +Database due:2024-03-15T18:00 ~8h type:urgent ticket:ENG-4739 created:2024-03-01T10:00 started:2024-03-10T09:00
  - [ ] (B) 2024-03-10 Update connection pooling @alice ~2h
  - [ ] (B) 2024-03-11 Add retry logic @bob ~3h
  - [x] (C) 2024-03-09 2024-03-10 Write migration @alice ~3h

- [ ] 2024-03-15T09:00 Daily status report @alice repeat:"weekdays at 9am" due:2024-03-15T09:30 ~30m type:report
```

**After Inheritance:**

- First task: `+Acme/Backend/Database`, `#work`, `#critical`
- Subtasks: Inherit parent + section + top-level metadata
- Daily report: `+Acme/Backend`, `#work`, `#critical`

---

## Date and Time Examples

```markdown
# TODO +MyProject

## Date-only
- [ ] (A) 2024-03-10 Start project planning
- [x] (B) 2024-03-01 2024-03-05 Complete requirements

## Time-specific
- [ ] 2024-03-15T09:00 Morning standup due:2024-03-15T09:30
- [ ] 2024-03-15T14:00 Client presentation due:2024-03-15T15:00

## With timezones
- [ ] 2024-03-15T09:00-05:00 Team call (EST)
- [ ] 2024-03-15T15:00+01:00 European meeting

## Full lifecycle
- [ ] (A) 2024-03-10T09:00 Implement OAuth2 @alice created:2024-03-01T10:00 started:2024-03-10T09:00 due:2024-03-15T18:00

## Recurring
- [ ] 2024-03-18T09:00 Weekly standup repeat:"every monday at 9am"
- [ ] 2024-04-01 Monthly review repeat:"first day of month"
```

**Result:** All tasks inherit `+MyProject` from top-level section.

---

## YAML Front Matter

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

---

## Custom Date Format

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

---

## Multi-File with Different Front Matter

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
