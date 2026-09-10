# QA Checklist YAML Format Specification

## Overview

The `qa-checklist.yaml` file is the shared state between `/success-criteria` (which generates it) and the QA checklist loop (which executes against it). It tracks every success criterion, its verification method, and its current status.

## Schema

```yaml
# Header — project metadata
project: string          # Project identifier (e.g., "<PRODUCT_REPO>")
created: date            # YYYY-MM-DD when checklist was generated
source_plan: string      # Path to the plan file used to generate criteria
max_retries: integer     # Max fix attempts per check before marking blocked (default: 3)
build_command: string    # Command that verifies the project compiles/builds

# Summary — updated by the checklist loop after each iteration
summary:
  total: integer
  passed: integer
  failed: integer
  blocked: integer
  pending: integer

# Checks — the actual success criteria
checks:
  - id: string           # Unique ID: {LETTER}{NUMBER} (e.g., A1, B3, H10)
    name: string         # Human-readable description
    type: enum           # "automated" or "code-audit"

    # For automated checks:
    command: string      # Exact bash command to run
    expected: string     # Expected output (see comparison modes below)
    comparison_mode: enum  # "exact" | "regex" | "numeric" | "exit_code" (explicit, don't infer from string)
    side_effects: boolean  # true if the check mutates state (writes to DB, calls external APIs, costs money). Default: false.
                           # the checklist loop runs side_effects checks ONCE at the end, outside the retry loop.
                           # Read-only checks (GET endpoints, build commands, file scans) are always false.

    # For code-audit checks:
    files: list[string]  # File paths to read
    criteria: string     # Precise condition to verify in the code

    # Status tracking (managed by the checklist loop):
    status: enum         # "pending" | "passed" | "failed" | "blocked"
    retries: integer     # Number of fix attempts so far
    blocked_reason: string|null  # Why this check can't be fixed (null if not blocked)
```

## Comparison Modes

Each automated check MUST have an explicit `comparison_mode` field. Do not infer the mode from the `expected` string, always set it explicitly.

| Mode | `comparison_mode` | `expected` value | Semantics |
|------|-------------------|------------------|-----------|
| Exact string | `exact` | `"200"` | stdout must exactly equal this string (trimmed) |
| Regex match | `regex` | `/No issues found/` | stdout must match this regex |
| Numeric >= | `numeric` | `">= 100"` | numeric stdout must satisfy the comparison |
| Exit code | `exit_code` | `"0"` | command's exit code must equal this number |

## Status Values

| Status | Meaning | Set by |
|--------|---------|--------|
| `pending` | Not yet attempted | success-criteria (initial) |
| `passed` | Check verified successfully | checklist loop |
| `failed` | Check attempted but failed | checklist loop |
| `blocked` | Cannot fix after max_retries attempts | checklist loop |

## ID Convention

- Letters = categories (A, B, C, ..., Z)
- Numbers = sequential within category (1, 2, 3, ...)
- IDs are stable, once assigned, never renumber
- When removing a check, mark it `blocked` with reason "ARCHIVED" rather than deleting
- When adding checks to an existing category, use the next available number

## Example

```yaml
project: <PRODUCT_REPO>
created: <YYYY-MM-DD>
source_plan: "<PLAN_PATH>"
max_retries: 3
build_command: "cd '<PRODUCT_REPO>' && npm run build"
summary:
  total: 3
  passed: 1
  failed: 1
  pending: 1

checks:
  - id: A1
    name: "The catalog page lists every ceramic mug in stock"
    type: automated
    command: "curl -s http://localhost:<APP_PORT>/api/mugs | jq 'length'"
    expected: "3"
    comparison_mode: exact
    side_effects: false
    status: passed

  - id: A2
    name: "Adding a mug to the cart updates the cart badge"
    type: code-audit
    files: [src/Cart.tsx]
    criteria: "Cart.tsx increments the count when addItem() runs, and the badge reads the same store."
    status: failed

  - id: B1
    name: "The build compiles with zero errors"
    type: automated
    command: "cd '<PRODUCT_REPO>' && npm run build"
    expected: "0"
    comparison_mode: exit_code
    side_effects: false
    status: pending
```
