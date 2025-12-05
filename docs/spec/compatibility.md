# Format Compatibility

Conversion rules between TaskMark and other task formats. See [specification.md](../specification.md) for the core spec.

---

## todo.txt Conversion

| Element | todo.txt | TaskMark | Conversion Rule |
|---------|----------|---------|----------------|
| Priority | `(A)` at start | `(A)` after state | Move after `- [ ]` |
| Contexts | `@context` | `#context` | Replace `@` with `#` |
| Projects | `+project` | `+project` | No change |
| Assignees | Not supported | `@user` | Add if needed |
| Dates | Creation date required | Optional | Remove if unwanted |
| Sections | Not supported | `## Section` | Group by project |

### Example Conversion

**todo.txt:**

```
(A) 2024-03-10 Call client +Sales @phone due:2024-03-15
x 2024-03-05 2024-03-01 Complete report +Reports
```

**TaskMark:**

```markdown
# TODO

## Sales
- [ ] (A) 2024-03-10 Call client +Sales #phone due:2024-03-15

## Reports
- [x] 2024-03-01 2024-03-05 Complete report +Reports
```

---

## todo.md Conversion

| Element | todo.md | TaskMark | Conversion Rule |
|---------|---------|---------|----------------|
| States | `[ ]`, `[x]`, `[-]` | `[ ]`, `[x]`, `[-]`, `[!]` | Add `[!]` if needed |
| Priority | Not standardized | `(value)` after state | Add if needed |
| Metadata | Freeform | `key:value` | Standardize format |
| Recurrence | Not supported | `repeat:PATTERN` | Add if needed |
| File links | Not supported | `[text](file.md)` | Add if needed |

### Example Conversion

**todo.md:**

```markdown
# Tasks

- [ ] Important task
- [x] Completed task
- [-] Cancelled task
```

**TaskMark:**

```markdown
# TODO

## Tasks
- [ ] (A) Important task
- [x] Completed task
- [-] Cancelled task
```

---

## xit Conversion

| Element | xit | TaskMark | Conversion Rule |
|---------|-----|---------|----------------|
| Date headers | `2024-03-15` | `## Section` | Convert to sections |
| States | `[ ]`, `[x]`, `[-]` | `[ ]`, `[x]`, `[-]`, `[!]` | Add `[!]` if needed |
| Metadata | `key:value` | `key:value` | Extract core fields |
| Priority | Not supported | `(value)` | Add if needed |
| Projects | `project:name` | `+name` | Convert syntax |

### Example Conversion

**xit:**

```
2024-03-15
[ ] Deploy to staging project:Backend
[x] Write tests project:Backend
[-] Skip documentation
```

**TaskMark:**

```markdown
# TODO

## 2024-03-15
- [ ] Deploy to staging +Backend
- [x] Write tests +Backend
- [-] Skip documentation
```

---

## Bidirectional Conversion Notes

| Direction | Data Loss | Notes |
|-----------|-----------|-------|
| todo.txt → TaskMark | None | Full compatibility |
| TaskMark → todo.txt | Sections, blocked state, subtasks | Use project tags for sections |
| todo.md → TaskMark | None | Full compatibility |
| TaskMark → todo.md | Priority, metadata, recurrence | Lossy conversion |
| xit → TaskMark | None | Full compatibility |
| TaskMark → xit | Sections, assignees | Use date headers |
