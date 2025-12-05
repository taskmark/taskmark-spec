# [1.2.0](https://github.com/taskmark/taskmark-spec/compare/v1.1.0...v1.2.0) (2025-12-05)

# [1.1.0](https://github.com/taskmark/taskmark-spec/releases/tag/v1.1.0) (2025-12-05)

- Add v1.1.0 frontmatter enhancements and modular spec structure
- Add date_format frontmatter field for custom output formatting
- Add field behavior table (locale, timezone, date_format)
- Document file-scoped frontmatter (no inheritance to linked files)
- Split specification.md into modular detail files:
  - spec/frontmatter.md: field behavior, processing, file scope
  - spec/examples.md: date/time, YAML, multi-file examples
  - spec/libraries.md: library stack and processing pipeline
  - spec/compatibility.md: todo.txt, todo.md, xit conversion
- Reduce specification.md from 656 to 508 lines (under AISD 600 limit)
- Configure semantic-release for docs-first repos (docs=minor)
- Auto-update version strings in markdown files on release

# [1.0.0](https://github.com/taskmark/taskmark-spec/releases/tag/v1.0.0) (2025-12-05)

- Markdown-based task format (.md files)
- Four task states: `[ ]` open, `[x]` done, `[-]` cancelled, `[!]` blocked
- Flexible priorities `(A-Z)` and numeric `(1-99)`
- Assignees with `@user` syntax
- Hierarchical projects `+project/sub` and tags `#tag`
- ISO 8601 datetime support with timezones
- Recurring tasks with natural language patterns (`repeat:weekly`)
- Section-based metadata inheritance
- Multi-file organization with markdown links
- Subtasks (one level deep)
- YAML frontmatter for file-level settings (`locale`, `timezone`)
- Complete technical specification
- Format compatibility guide (todo.txt, todo.md, xit)
- AI agent development guide (AISD-inspired)
- Library stack recommendations
