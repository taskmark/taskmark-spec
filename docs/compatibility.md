# Format Compatibility

Conversion rules. See [specification.md](../spec/specification.md).

---

## todo.txt

| Element | todo.txt | TaskMark |
|---------|----------|----------|
| States | `x` (done), none (open) | `[ ]`, `[.]`, `[x]`, `[-]`, `[!]` |
| Priority | `(A)` at start | `(A)` after state |
| Contexts | `@context` | `#context` |
| Projects | `+project` | `+project` |
| Assignees | None | `@user` |

---

## todo.md

| Element | todo.md | TaskMark |
|---------|---------|----------|
| States | `[ ]`, `[x]`, `[-]` | `[ ]`, `[.]`, `[x]`, `[-]`, `[!]` |
| Priority | None | `(value)` |
| Metadata | Freeform | `key:value` |
| Recurrence | None | `repeat:PATTERN` |

---

## xit

| Element | xit | TaskMark |
|---------|-----|----------|
| States | `[ ]`, `[@]`, `[x]`, `[~]`, `[?]` | `[ ]`, `[.]`, `[x]`, `[-]`, `[!]` |
| Priority | `!`, `!!`, `!!!` | `(C)`, `(B)`, `(A)` |
| Projects | None | `+name` |

State mapping: `[@]`→`[.]`, `[~]`→`[-]`, `[?]`→`[!]`

---

## Data Loss

| Direction | Lost |
|-----------|------|
| todo.txt → TaskMark | None |
| TaskMark → todo.txt | `[.]`/`[-]`/`[!]`, sections, subtasks, notes |
| todo.md → TaskMark | None |
| TaskMark → todo.md | `[.]`/`[!]`, priority, metadata, recurrence, notes |
| xit → TaskMark | `[?]` nuance |
| TaskMark → xit | Sections, assignees, projects, subtasks, notes |
