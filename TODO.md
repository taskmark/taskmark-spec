# TODO +taskmark-spec

Project roadmap and tasks for the TaskMark specification.

## v1.1.0 - Frontmatter Enhancements +taskmark-spec/v1.1.0 #documentation

### Locale and Date Handling

- [x] Document `locale` frontmatter field ~2h
  - Used for parsing natural language dates and recurrence patterns
  - When set (without `date_format`), output uses locale's default date format instead of ISO
- [x] Document `date_format` frontmatter field ~1h
  - Accepts strftime patterns (e.g., `%d/%m/%Y`)
  - Overrides locale default format when both are set
- [x] Document that default output format is ISO 8601 (`YYYY-MM-DD`) when no locale/date_format set ~30m

### File-Scoped Frontmatter

- [x] Document that `timezone` only applies to the current file ~30m
- [x] Document that `locale` only applies to the current file ~30m
- [x] Document that `date_format` only applies to the current file ~30m
- [x] Document that imported files retain their own frontmatter settings ~1h

## v1.2.0 - Documentation Updates +taskmark-spec/v1.2.0 #documentation

- [ ] Document `@user` assignees in headers for inheritance ~1h
- [ ] Document `.` support in project names (e.g., `+app/v1.2.3`) ~30m

## Backlog +taskmark-spec/backlog

- [ ] Reference parser implementation #implementation
- [ ] VSCode extension #tooling
- [ ] CLI tool for filtering/querying tasks #tooling
