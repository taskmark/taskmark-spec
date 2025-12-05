# Library Stack

Required libraries for TaskMark implementations. See [specification.md](../specification.md) for the core spec.

---

## Required Libraries

| Purpose | Library | Version | Usage |
|---------|---------|---------|-------|
| Natural language → datetime | [dateparser](https://dateparser.readthedocs.io/) | Latest | Parse phrases like "next Friday 3pm" |
| Natural language → recurrence | [recurrent](https://github.com/kvh/recurrent) | Latest | Parse "every Tuesday" into RRULE |
| Recurrence → dates | [python-dateutil](https://dateutil.readthedocs.io/) | Latest | Generate occurrence dates from RRULE |
| Datetime wrapper | [pendulum](https://pendulum.eustace.io/) | Latest | Timezone-aware datetime operations |

---

## Processing Pipeline

| Step | Input | Library | Output |
|------|-------|---------|--------|
| 1 | Natural language date | dateparser | Datetime object |
| 2 | Natural language recurrence | recurrent | RRULE string |
| 3 | RRULE string | python-dateutil | List of datetime objects |
| 4 | Datetime object | pendulum | Timezone-aware datetime |

---

## Library Details

### dateparser

Parses natural language date expressions into datetime objects.

**Capabilities:**

- Multi-language support (50+ languages)
- Relative dates: "tomorrow", "next week", "in 3 days"
- Absolute dates: "March 15, 2024", "15/03/2024"
- Time expressions: "3pm", "15:00", "noon"

**Usage in TaskMark:**

- Parsing natural language dates in task descriptions
- Respecting `locale` from YAML front matter

### recurrent

Parses natural language recurrence patterns into RRULE strings.

**Capabilities:**

- Pattern recognition: "every Tuesday", "daily", "weekly"
- Complex patterns: "first Monday of month", "every other week"
- Output: iCalendar RRULE format

**Usage in TaskMark:**

- Parsing `repeat:` field values
- Converting to RRULE for date calculation

### python-dateutil

Generates occurrence dates from RRULE strings.

**Capabilities:**

- Full RRULE support (RFC 5545)
- Date iteration: next occurrence, all occurrences in range
- Timezone handling

**Usage in TaskMark:**

- Calculating next occurrence when task is completed
- Generating future task instances

### pendulum

Timezone-aware datetime wrapper with intuitive API.

**Capabilities:**

- Timezone conversion
- Date arithmetic
- Formatting with locale support

**Usage in TaskMark:**

- Applying `timezone` from YAML front matter
- Date offset calculations for recurring tasks
- Formatting output dates
