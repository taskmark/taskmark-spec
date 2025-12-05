# [1.1.0](https://github.com/taskmark/taskmark-spec/compare/v1.0.0...v1.1.0) (2025-12-05)

# Changelog

## [1.0.0](https://github.com/taskmark/taskmark-spec/releases/tag/v1.0.0) (2025-12-05)

Initial release of the TaskMark specification.

### Features

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

### Documentation

- Complete technical specification
- Format compatibility guide (todo.txt, todo.md, xit)
- AI agent development guide (AISD-inspired)
- Library stack recommendations
