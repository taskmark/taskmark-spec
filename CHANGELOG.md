## [2.0.1](https://github.com/taskmark/taskmark-spec/compare/v2.0.0...v2.0.1) (2025-12-06)


### Bug Fixes

* **release:** update version sed patterns and bump to v2.0.0 ([676019a](https://github.com/taskmark/taskmark-spec/commit/676019ae5fd95ff7059af7518ac5b591ab4938f7))

# [2.0.0](https://github.com/taskmark/taskmark-spec/compare/v1.2.0...v2.0.0) (2025-12-06)


* docs(spec)!: reorganize repository and simplify date handling ([#6](https://github.com/taskmark/taskmark-spec/issues/6)) ([0f986fe](https://github.com/taskmark/taskmark-spec/commit/0f986fe6f89e55c9c5b7f71cb26cec073be64e7f))


### Bug Fixes

* **release:** add breaking change rule to trigger major version ([483fd52](https://github.com/taskmark/taskmark-spec/commit/483fd523db17065791c9ccf5867421ec62b21ad2))


### BREAKING CHANGES

* Multiple breaking changes to spec format

Date handling changes:
- Remove positional dates (bare YYYY-MM-DD after priority)
- All dates now require key:value syntax (planned:, due:, done:)
- Rename frontmatter field: date_format → datetime_format
- Add bracket syntax for optional time: %d/%m/%Y[ %H:%M]

Repository reorganization:
- Move specification from docs/ to spec/
- Move implementation docs (compatibility, libraries) to docs/
- Flatten tests/golden/ to tests/
- Remove examples/ (converted to golden tests T12-T15)

New content:
- Add golden test suite T01-T15 with full conformance coverage
- Add TESTING.md with test harness documentation
- Add CATALOG.md with detailed coverage matrix

Config updates:
- Update CI, pre-commit, and release configs for new paths
- Add docs(spec) scope rule for semantic release
- Exclude tests/ from linting (intentional edge cases)

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
