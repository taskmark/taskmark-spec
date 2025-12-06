# Implementation Libraries

Recommended libraries for TaskMark implementations. See [specification.md](../spec/specification.md) for core spec.

---

## Core Capabilities

TaskMark parsers need libraries for:

| Capability | Purpose |
|------------|---------|
| Natural language → datetime | Parse phrases like "next Friday 3pm" |
| Natural language → recurrence | Parse "every Tuesday" into RRULE |
| RRULE → dates | Generate occurrence dates from RRULE |
| Timezone handling | Timezone-aware datetime operations |

---

## Processing Pipeline

| Step | Input | Output |
|------|-------|--------|
| 1 | Natural language date | Datetime object |
| 2 | Natural language recurrence | RRULE string (RFC 5545) |
| 3 | RRULE string | List of datetime objects |
| 4 | Datetime object | Timezone-aware datetime |

---

## Language-Specific Recommendations

### Python

| Purpose | Library | Notes |
|---------|---------|-------|
| Natural language dates | [dateparser](https://dateparser.readthedocs.io/) | Multi-language support |
| Natural language recurrence | [recurrent](https://github.com/kvh/recurrent) | Parses to RRULE |
| RRULE processing | [python-dateutil](https://dateutil.readthedocs.io/) | Full RFC 5545 support |
| Datetime wrapper | [pendulum](https://pendulum.eustace.io/) | Timezone-aware operations |

### JavaScript/TypeScript

| Purpose | Library | Notes |
|---------|---------|-------|
| Natural language dates | [chrono-node](https://github.com/wanasit/chrono) | Multi-language support |
| RRULE processing | [rrule](https://github.com/jakubroztocil/rrule) | Full RFC 5545 support |
| Datetime handling | [luxon](https://moment.github.io/luxon/) | Timezone-aware operations |

### Go

| Purpose | Library | Notes |
|---------|---------|-------|
| RRULE processing | [teambition/rrule-go](https://github.com/teambition/rrule-go) | RFC 5545 support |
| Datetime handling | Standard `time` package | Built-in timezone support |

### Rust

| Purpose | Library | Notes |
|---------|---------|-------|
| RRULE processing | [rrule](https://crates.io/crates/rrule) | RFC 5545 support |
| Datetime handling | [chrono](https://crates.io/crates/chrono) | Timezone-aware operations |

---

## Implementation Notes

Implementations SHOULD:

1. Use ISO 8601 for all date parsing and serialization
2. Support RRULE (RFC 5545) for recurrence patterns
3. Handle timezone conversion correctly
4. Preserve date precision (date-only vs datetime vs with timezone)
