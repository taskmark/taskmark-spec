# Golden Testing Framework

This document describes how golden tests work for TaskMark conformance testing.

---

## Overview

Golden tests verify that a TaskMark implementation correctly:

1. **Parses** markdown files into a structured data store
2. **Mutates** tasks according to mutation instructions
3. **Serializes** the modified data back to markdown files

Each test is a self-contained universe with its own files, expected parse output, mutations, and expected serialized output.

---

## Test Structure

```
tests/golden/
├── CATALOG.md              # Test catalog and coverage documentation
├── TESTING.md              # This file
└── T{NN}_{name}/
    ├── input.md            # Root file to parse (required)
    ├── input_{linked}.md   # Linked files (optional, 0 or more)
    ├── parsed.yaml         # Expected parse output (required)
    ├── mutation.yaml       # Mutations to apply (optional)
    ├── mutated.md          # Expected output after mutation (required if mutation.yaml exists)
    └── mutated_{linked}.md # Expected linked file outputs (required if linked files exist and mutation.yaml exists)
```

---

## Test Execution Flow

### Phase 1: Setup

```
┌─────────────────────────────────────────────────────────────┐
│                     TEST DIRECTORY                          │
│  T06_frontmatter/                                           │
│    input.md                                                 │
│    input_us_office.md                                       │
│    input_uk_office.md                                       │
│    parsed.yaml                                              │
│    mutation.yaml                                            │
│    mutated.md                                               │
│    mutated_us_office.md                                     │
│    mutated_uk_office.md                                     │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                     TEMP DIRECTORY                          │
│  /tmp/taskmark_test_xyz/                                    │
│    input.md              ← copied from input.md             │
│    us_office.md          ← copied from input_us_office.md   │
│    uk_office.md          ← copied from input_uk_office.md   │
└─────────────────────────────────────────────────────────────┘
```

**File naming transformation:**

- `input.md` → `input.md` (no change)
- `input_{name}.md` → `{name}.md` (remove `input_` prefix)

This simulates a real project structure where linked files don't have test prefixes.

### Phase 2: Parse

```
┌──────────────────┐         ┌──────────────────────────────────┐
│   TEMP FILES     │         │        TasksDataStore            │
│                  │         │                                  │
│  input.md        │         │  files: [...]                    │
│  us_office.md    │──parse──│  tasks: [...]                    │
│  uk_office.md    │         │  file_links: [...]               │
│                  │         │                                  │
└──────────────────┘         └──────────────────────────────────┘
                                            +
                             ┌──────────────────────────────────┐
                             │         ParseResult              │
                             │                                  │
                             │  warnings: [...]                 │
                             │  errors: [...]                   │
                             │  malformed_lines: [...]          │
                             └──────────────────────────────────┘
```

The parser reads `input.md` (the root file), follows any `[[file.md]]` links, and returns:

- `TasksDataStore` - the parsed data structure
- `ParseResult` - warnings, errors, and malformed lines encountered during parsing

### Phase 3: Verify Parse Output

```
┌──────────────────────────────────┐     ┌──────────────────────────────────┐
│        TasksDataStore            │     │         ParseResult              │
│                                  │     │                                  │
│  files: [...]                    │     │  warnings: [...]                 │
│  tasks: [...]                    │     │  errors: [...]                   │
│  file_links: [...]               │     │  malformed_lines: [...]          │
└──────────────────────────────────┘     └──────────────────────────────────┘
                │                                       │
                │              COMPARE                  │
                └──────────────────┬────────────────────┘
                                   │
                                   ▼
                        ┌──────────────────────────────────┐
                        │         parsed.yaml              │
                        │                                  │
                        │  tasks: [...]                    │
                        │  file_links: [...]               │
                        │  frontmatter: {...}              │
                        │  warnings: [...]                 │
                        │  errors: [...]                   │
                        │  malformed_lines: [...]          │
                        └──────────────────────────────────┘
                                   │
                                   ▼
                              PASS/FAIL
```

**Comparison rules:**

- `parsed.yaml` contains expected values from BOTH `TasksDataStore` AND `ParseResult`
- Every field in `parsed.yaml` MUST exist in the corresponding structure
- Every field in `parsed.yaml` MUST have matching value
- `TasksDataStore` and `ParseResult` MAY have additional fields not in `parsed.yaml`
- `parsed.yaml` is a **subset** of the combined output

### Phase 4: Mutate (if mutation.yaml exists)

```
┌──────────────────────────────────┐     ┌──────────────────┐
│        TasksDataStore            │     │  mutation.yaml   │
│        (from parse)              │     │                  │
│                                  │     │  mutations: [...] │
│  files: [...]                    │     │  options:        │
│  tasks: [...]                    │     │    today: "..."  │
│  file_links: [...]               │     │                  │
└──────────────────────────────────┘     └──────────────────┘
                │                                   │
                └───────────────┬───────────────────┘
                                │
                                ▼
                      ┌──────────────────┐
                      │     mutate()     │
                      └──────────────────┘
                                │
                ┌───────────────┴───────────────┐
                ▼                               ▼
┌──────────────────────────────────┐     ┌──────────────────────────────────┐
│     TasksDataStore               │     │      MutationResult              │
│     (modified)                   │     │                                  │
│                                  │     │  results: [                      │
│  files: [...]                    │     │    { status: success },          │
│  tasks: [...] (updated)          │     │    { status: error,              │
│  file_links: [...]               │     │      error: "Task not found" }   │
│                                  │     │  ]                               │
└──────────────────────────────────┘     └──────────────────────────────────┘
```

**Mutation behavior:**

- All mutations in `mutation.yaml` are applied together (atomically)
- `options.today` and other options are passed to the mutator for deterministic behavior
- The mutator returns TWO objects:
  - `TasksDataStore` - the modified data structure
  - `MutationResult` - status of each mutation (success or error)

### Phase 5: Serialize

```
┌──────────────────────────────────┐
│     TasksDataStore               │
│     (after mutation)             │
│                                  │
│  files: [...]                    │
│  tasks: [...] (updated)          │
│  file_links: [...]               │
└──────────────────────────────────┘
                │
                ▼
        ┌──────────────┐
        │  serialize() │
        └──────────────┘
                │
                ▼
┌──────────────────────────────────┐
│         TEMP FILES               │
│         (modified in place)      │
│                                  │
│  input.md      (updated)         │
│  us_office.md  (updated)         │
│  uk_office.md  (updated)         │
└──────────────────────────────────┘
```

**Serialization rules:**

- The serializer receives ONLY the `TasksDataStore`
- Files are modified **in place** in the temp directory
- Only task lines and their children (subtasks, notes) are modified
- **Frontmatter is NOT touched** by the serializer
- Non-task content (headers, plain text, malformed lines) is preserved

### Phase 6: Verify Serialized Output

```
┌──────────────────────────────────┐     ┌──────────────────────────────────┐
│         TEMP FILES               │     │      EXPECTED FILES              │
│         (after serialize)        │     │      (from test dir)             │
│                                  │     │                                  │
│  input.md                        │────▶│  mutated.md                      │
│  us_office.md                    │     │  mutated_us_office.md            │
│  uk_office.md                    │     │  mutated_uk_office.md            │
└──────────────────────────────────┘     └──────────────────────────────────┘
                                                        │
                                                        ▼
                                               COMPARE (normalized)
                                                        │
                                                        ▼
                                                   PASS/FAIL
                                                  (+ warnings)
```

**File mapping for comparison:**

- `input.md` (temp) ↔ `mutated.md` (test dir)
- `{name}.md` (temp) ↔ `mutated_{name}.md` (test dir)

**Comparison rules:**

- Comparison is **normalized** to prevent false failures:
  - Trailing whitespace ignored
  - Trailing newlines normalized
  - Line endings normalized (CRLF → LF)
- If normalized content matches: **PASS**
- If normalized content matches but byte-for-byte differs: **PASS with WARNING**
- If normalized content differs: **FAIL**

---

## Data Structures

### TasksDataStore

The central data structure produced by parsing. Contains ONLY the successfully parsed data.

```yaml
TasksDataStore:
  # Files that were parsed
  files:
    - path: "input.md"                    # Relative path
      frontmatter:                        # Parsed frontmatter (null if none)
        locale: "en_US"
        timezone: "America/New_York"
        date_format: "%B %d, %Y"
      content: "..."                      # Raw file content (for serialization)

  # All tasks across all files
  tasks:
    - id: "task_001"                      # Unique identifier
      title: "Task description"
      state: open                         # open | in_progress | done | cancelled | blocked

      # Location
      file: "input.md"
      line: 5
      indent: 0

      # Metadata - combined (inherited + local + downstream)
      priority: "A"
      project_path: "Project/Sub"
      assignees: ["alice", "bob"]
      tags: ["urgent", "backend"]
      estimate_minutes: 120

      # Dates
      created_date: "2024-03-01"
      planned_date: "2024-03-10"
      started_date: "2024-03-10"
      paused_date: null
      due_date: "2024-03-15"
      done_date: null

      # Recurrence
      recurrence: "weekly"                # null if none

      # Custom fields
      custom_fields:
        type: "bug"
        ticket: "ENG-123"

      # Metadata provenance - tracks where each attribute came from
      inherited:                          # From parent sections/headers
        project_path: "Project"
        assignees: ["alice"]
        tags: ["backend"]
        custom_fields:
          severity: "high"

      local:                              # From the task line itself
        priority: "A"
        project_path: "Sub"               # Joins with inherited: Project/Sub
        assignees: ["bob"]
        tags: ["urgent"]
        estimate_minutes: 120
        due_date: "2024-03-15"

      downstream:                         # Propagated from subtasks
        assignees: ["charlie"]            # Subtask had @charlie
        tags: ["critical"]                # Subtask had #critical

      # Children
      subtasks:
        - id: "subtask_001"
          title: "Subtask description"
          state: open
          file: "input.md"
          line: 6
          indent: 2
          # ... same structure as parent task

      notes:
        - text: "Note content"
          file: "input.md"
          line: 7
          has_repeat_tag: false

  # File links discovered during parsing
  file_links:
    - source: "input.md"
      target: "us_office.md"
      section: "US Office"
      line: 4
```

### ParseResult

Returned alongside TasksDataStore from the parser. Contains warnings, errors, and malformed lines.

```yaml
ParseResult:
  # Parser warnings (non-fatal issues)
  warnings:
    - file: "input.md"
      line: 10
      code: "W001"
      message: "Duplicate tag '#urgent' ignored"

  # Parser errors (fatal issues for specific items)
  errors:
    - file: "input.md"
      line: 15
      code: "E001"
      message: "Invalid state character 'y'"

  # Malformed lines (preserved but not parsed as tasks)
  malformed_lines:
    - file: "input.md"
      line: 14
      content: "- [] Missing space after bracket"
      reason: "No space after opening bracket"
```

### parsed.yaml Schema

The expected parse output file. A **subset** of TasksDataStore AND ParseResult combined.

```yaml
# Comment describing what this test covers

tasks:
  - title: "Task description"
    state: open
    file: input.md
    line: 5
    indent: 0
    # Only include fields being tested
    # Omitted fields are not verified

# Optional sections - only include what you're testing
file_links:
  - source: input.md
    target: us_office.md
    section: "US Office"
    line: 4

frontmatter:
  us_office.md:
    locale: en_US
    timezone: America/New_York
    date_format: "%B %d, %Y"

warnings:
  - file: input.md
    line: 10
    code: "W001"
    message: "Duplicate tag ignored"

errors:
  - file: input.md
    line: 15
    code: "E001"
    message: "Invalid state character"

malformed_lines:
  - line: 14
    content: "- [] Missing space"
    reason: "No space after opening bracket"
```

### mutation.yaml Schema

Mutations to apply after parsing.

```yaml
# Comment describing mutation behavior being tested

mutations:
  - target:
      title: "Task description"          # Match by title (simple case)
    changes:
      state: done
    expected_result:
      status: success

  - target:
      file: "us_office.md"               # Match by file + title (multi-file)
      title: "Quarterly review"
    changes:
      state: done
    expected_result:
      status: success

  - target:
      title: "Nonexistent task"
    changes:
      state: done
    expected_result:
      status: error
      error_code: "E100"
      error_message: "Task not found"

# Options passed to mutator for deterministic behavior
options:
  today: "2024-03-15"                    # Fixed date for done:/started:/paused:
  now: "2024-03-15T10:30:00Z"            # Fixed datetime (optional)
```

### MutationResult Schema

Result returned by the mutator.

```yaml
MutationResult:
  results:
    - target:
        title: "Task description"
      status: success
      changes_applied:
        state: done
        done_date: "2024-03-15"          # Auto-added

    - target:
        title: "Nonexistent task"
      status: error
      error_code: "E100"
      error_message: "Task not found"

  # Summary
  total: 3
  succeeded: 2
  failed: 1
```

---

## Warning and Error Codes

### Parser Warnings (Wxxx)

| Code | Description |
|------|-------------|
| W001 | Duplicate tag ignored |
| W002 | Duplicate assignee ignored |
| W003 | Duplicate custom field (last value used) |
| W004 | Duplicate date field (last value used) |
| W005 | Mixed indentation (tabs and spaces) |

### Parser Errors (Exxx)

| Code | Description |
|------|-------------|
| E001 | Invalid state character |
| E002 | Malformed checkbox (no space after bracket) |
| E003 | Invalid date format |
| E004 | Invalid estimate format |
| E005 | Linked file not found |

### Mutation Errors (E1xx)

| Code | Description |
|------|-------------|
| E100 | Task not found |
| E101 | Ambiguous target (multiple tasks match) |
| E102 | Invalid state transition |
| E103 | Cannot modify inherited metadata |

---

## Test Runner Pseudocode

```python
def run_golden_test(test_dir):
    # Phase 1: Setup
    temp_dir = create_temp_directory()
    copy_file(test_dir / "input.md", temp_dir / "input.md")
    for file in glob(test_dir / "input_*.md"):
        name = file.name.replace("input_", "")
        copy_file(file, temp_dir / name)

    # Phase 2: Parse
    data_store, parse_result = parse(temp_dir / "input.md")

    # Phase 3: Verify Parse
    expected = load_yaml(test_dir / "parsed.yaml")
    # Compare TasksDataStore fields
    assert_subset_match(expected, data_store, keys=["tasks", "files", "file_links", "frontmatter"])
    # Compare ParseResult fields
    assert_subset_match(expected, parse_result, keys=["warnings", "errors", "malformed_lines"])

    # Phase 4: Mutate (if mutation.yaml exists)
    mutation_file = test_dir / "mutation.yaml"
    if mutation_file.exists():
        mutations = load_yaml(mutation_file)
        data_store, mutation_result = mutate(data_store, mutations["mutations"], mutations["options"])
        verify_mutation_results(mutation_result, mutations["mutations"])

        # Phase 5: Serialize
        serialize(data_store)

        # Phase 6: Verify Output
        compare_normalized(temp_dir / "input.md", test_dir / "mutated.md")
        for file in glob(test_dir / "mutated_*.md"):
            name = file.name.replace("mutated_", "")
            compare_normalized(temp_dir / name, file)

    # Cleanup
    cleanup_temp_directory(temp_dir)

def assert_subset_match(expected, actual):
    """Verify all fields in expected exist and match in actual."""
    for key, value in expected.items():
        assert key in actual, f"Missing key: {key}"
        if isinstance(value, dict):
            assert_subset_match(value, actual[key])
        elif isinstance(value, list):
            assert_list_match(value, actual[key])
        else:
            assert actual[key] == value, f"Mismatch for {key}"

def compare_normalized(actual_file, expected_file):
    """Compare files with normalization."""
    actual = read_file(actual_file)
    expected = read_file(expected_file)

    actual_normalized = normalize(actual)
    expected_normalized = normalize(expected)

    if actual_normalized != expected_normalized:
        raise AssertionError(f"Content mismatch:\n{diff(actual, expected)}")

    if actual != expected:
        emit_warning(f"Byte-level difference in {actual_file} (normalized match)")

def normalize(content):
    """Normalize content for comparison."""
    # Normalize line endings
    content = content.replace("\r\n", "\n")
    # Remove trailing whitespace from lines
    lines = [line.rstrip() for line in content.split("\n")]
    # Remove trailing empty lines
    while lines and lines[-1] == "":
        lines.pop()
    return "\n".join(lines)
```

---

## Example Test Walkthrough

### T01_minimal

**Test directory contents:**

```
T01_minimal/
  input.md
  parsed.yaml
  mutation.yaml
  mutated.md
```

**Phase 1: Setup**

```
/tmp/test_xyz/
  input.md          ← copy of T01_minimal/input.md
```

**Phase 2: Parse** `input.md`:

```markdown
# Tasks

- [ ] Simple task
- [ ] Another task
```

**Returns two objects:**

`TasksDataStore`:

```yaml
files:
  - path: "input.md"
    frontmatter: null
tasks:
  - title: "Simple task"
    state: open
    file: input.md
    line: 3
    indent: 0
  - title: "Another task"
    state: open
    file: input.md
    line: 4
    indent: 0
file_links: []
```

`ParseResult`:

```yaml
warnings: []
errors: []
malformed_lines: []
```

**Phase 3: Verify** against `parsed.yaml`:

```yaml
tasks:
  - title: Simple task
    state: open
    file: input.md
    line: 3
    indent: 0
  - title: Another task
    state: open
    file: input.md
    line: 4
    indent: 0
```

✓ All fields match

**Phase 4: Mutate** using `mutation.yaml`:

```yaml
target:
  title: "Simple task"
changes:
  state: done
options:
  today: "2024-03-15"
```

**Phase 5: Serialize** to temp files

**Phase 6: Compare** `/tmp/test_xyz/input.md` to `T01_minimal/mutated.md`:

```markdown
# Tasks

- [x] Simple task done:2024-03-15
- [ ] Another task
```

✓ Match

---

## Best Practices for Writing Golden Tests

1. **One concept per test**: Each test should focus on a single feature or behavior
2. **Minimal input**: Use the smallest input that demonstrates the behavior
3. **Explicit expectations**: Only include fields in `parsed.yaml` that you want to verify
4. **Deterministic dates**: Always use `options.today` in mutations
5. **Document intent**: Add comments at the top of `parsed.yaml` and `mutation.yaml`
6. **Cover edge cases**: Include malformed input to test error handling
7. **Test both success and failure**: Include mutations that should error
