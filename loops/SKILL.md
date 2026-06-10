---
name: loops
description: |
  Reference guide for autonomous loop patterns in agent development. Six named patterns
  from simple to complex: Sequential Pipeline, Implement-Test-Fix, De-Sloppify,
  Continuous PR, Research-Implement-Verify, Multi-Agent Orchestration.
  Use when: "agent loop", "autonomous loop", "loop pattern", "sequential pipeline",
  "multi-agent pattern", "de-sloppify", "agent patterns", "implement-test-fix loop".
  PROACTIVE TRIGGER: When designing agent workflows.
allowed-tools:
  - Bash
  - Read
  - Write
  - Edit
  - Grep
  - Glob
  - Agent
last_used: "2026-05-18"
gotchas_count: 0
---

# Loops — Autonomous Agent Loop Patterns

Named patterns for structuring agent work, from simple command chaining to
multi-agent orchestration. Reference this when designing workflows or when
a task maps to one of these patterns.

## Decision Matrix

| Pattern | Use When | Complexity | Duration |
|---------|----------|------------|----------|
| Sequential Pipeline | Quick transformations, format conversions | Low | Seconds |
| Implement-Test-Fix | Any feature with tests | Medium | Minutes |
| De-Sloppify | After implementation, before PR | Medium | Minutes |
| Continuous PR | Batch processing well-defined issues | High | Hours |
| Research-Implement-Verify | Unfamiliar APIs/libraries | High | 30+ min |
| Multi-Agent Orchestration | Large features, many files | Very High | Hours |

## Pattern 1: Sequential Pipeline

**What**: Chain prompts where output of one becomes input of the next.

**When**: Format conversions, data transformations, quick multi-step processing.

**Steps**:
1. Define the pipeline stages
2. Run each stage, passing output forward
3. No branching or error recovery

**Example**:
```bash
# Extract → Transform → Format
claude -p "Extract all function signatures from src/api.ts" | \
claude -p "Convert these to OpenAPI schema definitions" | \
claude -p "Format as YAML"
```

**Anti-patterns**: Using this for anything with error handling needs. If any stage can fail, use Implement-Test-Fix instead.

---

## Pattern 2: Implement-Test-Fix Loop

**What**: The core development loop. Build, test, fix, repeat until green.

**When**: Any feature implementation where tests exist or will be written.

**Steps**:
1. Implement the feature (or write failing tests first for TDD)
2. Run the test suite
3. If tests pass → done
4. If tests fail → read the error, analyze root cause, fix
5. Go to step 2

**Termination**: All tests pass, or max iterations reached (default: 10).

**Example**:
```
Implement the user authentication middleware.
After each change, run `npm test`.
If tests fail, analyze the error and fix it.
Repeat until all tests pass.
Maximum 10 iterations.
```

**Anti-patterns**:
- Fixing the test instead of the code
- Not reading the actual error message before attempting a fix
- Changing unrelated code in the fix step
- No iteration limit (infinite loops waste tokens)

---

## Pattern 3: De-Sloppify

**What**: Dedicated post-implementation cleanup pass. Separate from code review. Catches the mess that accumulates during fast implementation.

**When**: After any substantial implementation, before creating a PR. The "second pass" that turns working code into clean code.

**Steps**:
1. Read ALL changed files (`git diff --name-only`)
2. For each file, check for:
   - Dead code (unused imports, unreachable branches, commented-out code)
   - Inconsistent naming (mixed camelCase/snake_case within a file)
   - Leftover debug statements (`console.log`, `print(`, `debugger`)
   - TODO/FIXME comments that should be resolved or tracked
   - Hardcoded values that should be constants or config
   - Copy-pasted code that should be extracted into a function
   - Missing error handling on the happy path
   - Overly complex functions (>30 lines) that should be split
3. Fix each issue
4. Re-run tests to confirm no regressions
5. Run `/verify` to confirm everything still passes

**Termination**: No more issues found across all files, and tests pass.

**Example**:
```
Read all files I changed in this session.
For each file, look for dead code, debug statements, hardcoded values,
copy-paste duplication, and inconsistent naming.
Fix each issue, then re-run tests.
Continue until no issues remain.
```

**Anti-patterns**:
- De-sloppifying files you didn't change (scope creep)
- Refactoring architecture during cleanup (that's a separate task)
- Skipping the test re-run after fixes

---

## Pattern 4: Continuous PR Loop

**What**: Auto-process a batch of issues into PRs. Each issue gets its own branch, implementation, tests, and PR.

**When**: Sprint with many well-defined tickets. Issues must have clear requirements.

**Steps**:
1. Read the list of issues (from Linear, GitHub, or a markdown file)
2. For each issue:
   a. Create a branch from main
   b. Read the issue description
   c. Plan the implementation
   d. Implement (using Implement-Test-Fix loop)
   e. Run `/verify`
   f. Create PR with description linking to the issue
3. Report: which PRs created, which issues had problems

**Termination**: All issues processed or max issues reached.

**Prerequisites**:
- Issues must be well-defined (clear acceptance criteria)
- Each issue must be independent (no cross-dependencies)
- Tests must exist or be part of the implementation

**Anti-patterns**:
- Processing issues that depend on each other
- Skipping verification to go faster
- Creating PRs without running tests
- Not linking PRs back to issues

---

## Pattern 5: Research-Implement-Verify

**What**: For unfamiliar territory where you need to learn before building. Research first, then implement incrementally with verification at each step.

**When**: Integrating new APIs, using unfamiliar libraries, implementing algorithms you haven't used before.

**Steps**:
1. **Research** — Read docs, examples, and existing code
   - Check official documentation
   - Read existing usage in the codebase
   - Find example implementations online
2. **Plan** — Design the approach based on research
   - Identify the minimal viable implementation
   - List unknowns and risks
3. **Implement Incrementally** — Build in small, verifiable chunks
   - Implement one piece
   - Verify it works (manual test, unit test, or output check)
   - If verification fails → go back to research
   - Continue to next piece
4. **Final Verification** — Run full test suite + integration check

**Termination**: All pieces implemented and verified, full suite passes.

**Anti-patterns**:
- Implementing the whole thing before verifying any part
- Skipping research and guessing at API behavior
- Not verifying incrementally (building a house of cards)

---

## Pattern 6: Multi-Agent Orchestration

**What**: Delegate work to specialist agents that run in parallel. A coordinator merges results.

**When**: Large features touching many independent modules. The work can be parallelized.

**Steps**:
1. **Planner** creates the task breakdown with dependency graph
2. **Coordinator** assigns independent tasks to specialist agents
3. **Specialists** work in parallel (each in its own worktree or branch)
   - Each specialist uses Implement-Test-Fix internally
   - Each produces a PR or patch
4. **Reviewer** checks each specialist's output
5. **Coordinator** merges results, resolves conflicts

**Roles**:
- `Planner`: Architecture, task decomposition, dependency analysis
- `Implementer` (N instances): Feature implementation per module
- `Reviewer`: Code review, quality check
- `Coordinator`: Merge, conflict resolution, integration testing

**Termination**: All tasks complete, integration tests pass, coordinator approves.

**Anti-patterns**:
- Parallel agents modifying the same files (merge hell)
- No integration testing after merging
- Specialists making architectural decisions (that's the planner's job)
- Over-orchestrating simple tasks (use Implement-Test-Fix for 1-module changes)

---

## Combining Patterns

Patterns compose naturally:

- **Implement-Test-Fix + De-Sloppify**: Build it, make it work, then clean it up
- **Research-Implement-Verify + De-Sloppify**: Learn, build carefully, then polish
- **Continuous PR + Multi-Agent**: Each issue gets its own specialist agent
- **Any pattern + /verify**: Always end with verification before shipping

## Quick Reference

```
/loops              — show this reference
/loops itf          — Implement-Test-Fix details
/loops deslop       — De-Sloppify details
/loops cpr          — Continuous PR details
/loops research     — Research-Implement-Verify details
/loops multi        — Multi-Agent Orchestration details
```
