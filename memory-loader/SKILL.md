---
name: memory-loader
description: "MANDATORY session start protocol. Verifies vault infrastructure and loads all memory layers. Triggers on: session start, 'load context', 'start session', 'what do you remember', 'check memory', 'Read memory at...', or at the start of any substantive coding/vault/agent session. Must run BEFORE any work begins."
write_scope: self-only
version: 3.1.0
author: the repository owner
contributors: []
---

# Memory Loader, Session Start Protocol

MANDATORY at the start of every substantive session. Verifies vault infrastructure, loads memory, and gets the agent
oriented before any work begins. **If you find yourself working without having run this, STOP and run it.**

**This protocol is a token-budget rewrite.** An earlier version read far more per session than it needed, and
two of its reads were being silently truncated by the per-file cap, so it paid full price for half the
information. Every read below is bounded, and the total is kept small.

## When to use
Start of every substantive session; a new conversation with a chat-based agent; resuming after any gap; when the user
pastes a "Read memory at..." prompt; when context seems missing or stale; when switching projects mid-session.

## Cross-platform
Same protocol everywhere, one directory per agent: each agent reads and writes only `agents/<agent>/`. An
agent on a platform that cannot load this file inlines a compact version at the top of its own identity file.
This file is the canonical reference.
Vault root: `<HOME>/Documents/<NOTES_ROOT>/`.

---

## Step 0, Vault infrastructure

**Detect the project** from cwd via the project registry in `<AGENT_CONFIG>`. If cwd is the vault itself, do NOT
treat the session as project-neutral: the active project is whichever registry repo had a branch touched most
recently, unless the owner says otherwise. Do not surface a cross-project menu unless it is asked for.

**Verify the `<NOTES_LINK>/` symlink** in the project root (`ls -la <project-root>/<NOTES_LINK>`). If missing:
`ln -s <HOME>/Documents/<NOTES_ROOT>/projects/<name> <project-root>/<NOTES_LINK>` (create the vault folder first if
needed). This step is what keeps a project's notes visible to the notes app.

**Content directories**: per the house rule a project repo legitimately owns real `docs/` (with `docs/plans/` and
`docs/decisions/`), `research/`, `working/`, `deliverables/`, `assets/`, and `scratch/` directories, those are
correct and must NOT be moved. The thing to catch is a project that keeps **vault-scoped context notes** (project
overview, status, cross-session context) outside `<NOTES_LINK>/`, where the notes app cannot see them. If you find those, ask
the owner before moving anything: a wrong move breaks live paths. Never relocate `docs/plans/`, the house rule puts plans there.

**GitHub remote** (real projects only): `git remote -v`. No remote and not a scratch folder → create a private repo
under your own account or organization, add origin, push, and record the URL in the project overview. Never commit secrets, `.env`,
caches, or generated bulk media.

**Disk probe:** run a free-space check next to the dataless probe. A nearly full root volume breaks subagent
output files, image renders and sync jobs, and it surfaces as an unrelated red error rather than as "out of
disk". Say the number in the memory-loaded line and tell every dispatch the disk is tight until it is
cleared. Emptying the Trash is the owner's call, not the agent's.

## Step 0b, Open today's learning journal (MANDATORY)

Path: `agents/<agent>/learning-journals/YYYY-MM-DD-<short-topic>.md`, topic derived from the project.
If it exists, append `## Session resume — HH:MM`. If it does not exist, create it with exactly this header:

```markdown
---
type: learning-journal
date: YYYY-MM-DD
topic: <short-topic>
created: YYYY-MM-DD
project: <project-name>
branch: <git branch or "n/a">
tags: [learning-journal, <agent-name>, <project-slug>]
---

# Learning Journal — YYYY-MM-DD — <topic>

Real-time observations. Entries appended IMMEDIATELY when each event happens, NOT batched at wrap-up.

Format: `- [HH:MM] [category] one-to-three sentences + relevant file path or commit SHA`
Categories: `mistake`, `correction`, `worked`, `didnt-work`

## Entries
```

Append entries **immediately** as things happen, never batched at wrap-up. Format:
`- [HH:MM] [category] one to three sentences + file path or SHA`, categories `mistake` / `correction` / `worked` /
`didnt-work`. `/wrapup` consolidates the journal into corrections, patterns, and context.md.

## Step 0.5, Shared memory routing

Set `AGENT` to YOUR OWN agent name first; every command below reuses it.

```bash
AGENT=claude-code   # or: codex. Use your own, never another agent's
python3 <HOME>/Documents/<NOTES_ROOT>/<memory-tools>/memory-store load --agent "$AGENT" --json
```
Read the `source` field:
- **`shared`** (the normal state): load corrections / session-index / patterns / decisions / conflicts
  from the SHARED paths in the returned `read` map. `context.md`, `identity.md`, `learning-journals/` stay per-agent.
- **`union`**: same shared paths, but the helper fell back to a union of mirrors. Surface
  `⚠ shared memory fell back to union-of-mirrors (reason: <detail.reason>)`.
- **`legacy`** (switch absent, rollback state): read your own agent dir instead.

When source is `shared` or `union`, also read `<memory-store>/conflicts.md` and surface any unresolved keep-both
conflicts at the TOP of your context brief. Add `shared memory: <source>` to your "memory loaded" line.

## Step 1, context.md (read in full; it is budgeted to fit)

`agents/<agent>/context.md`. Hard-capped at a size that can be read whole. Check the
`<!-- last-session: -->` header; if more than 7 days old, flag `⚠ STALE MEMORY: last session was YYYY-MM-DD`.

If the file has grown past its budget, that is a bug to fix at wrap-up, not something to work around by reading less.

## Step 1a, The OTHER agent's context.md (cross-agent visibility)

Read `agents/<other-agent>/context.md` too (Claude reads Codex's, Codex reads Claude's). `context.md` is the one
memory kind that stays per-agent, so this is the only way you see what the other agent is mid-way through. Required by the house session-start protocol.

## Step 1b, identity.md (conditional, skip most sessions)

Read `agents/<agent>/identity.md` only if this is the first session of the day, or it has not been read in 7 days, or
the owner asks what you remember about them. It changes rarely; loading it every session wastes budget.

## Step 1c, Corrections (bounded slice, never the whole file)

```bash
python3 <HOME>/Documents/<NOTES_ROOT>/<memory-tools>/consolidator tail -n 5 --text
```
Returns the last 5 not-yet-promoted corrections plus every UNRESOLVED one. The full `corrections.md` grows without bound and
loads ONLY on an explicit search ("have we seen this mistake before?", retro audits, post-incident review). If
`consolidator` is unavailable, fall back to the last ~120 lines of `corrections.md`.

**Consolidator freshness**: check `<memory-store>/archive/consolidations/last-run.json`. Missing, many days stale, or
a verdict that is not PASS/WARN → also load every UNRESOLVED correction and surface
`⚠ consolidator not proven current`.

## Step 2, Session index (BOUNDED: newest entries only)

`session-index.md` grows without bound, and a full read is truncated anyway, so "read it all" never actually
worked. **The file is OLDEST-FIRST, so the newest entries are at the END, use `tail`, not `head`.**

```bash
IDX=$(python3 <HOME>/Documents/<NOTES_ROOT>/<memory-tools>/memory-store load --agent "$AGENT" --json \
      | python3 -c 'import json,sys;print(json.load(sys.stdin)["read"]["session-index"])')
tail -120 "$IDX"
```
Set `AGENT` to your own agent name (`claude-code`, `codex`, …), do not hardcode another agent's.

Older history is reachable on demand: grep that same file for a project name or date when a specific past session
actually matters. Full narratives live in `session-logs/`.

## Step 2b, resuming a handoff from another surface

If the current prompt carries a handoff from somewhere else (another agent's note, a chat
thread, a ticket), search your session index and session logs for the exact identifiers it
names before acting: the project name, and whatever id the handoff itself uses. A bounded
newest-entries read can miss the specific thread being resumed.

Read every matching handoff, including its artifact paths and its open-item owner. The other
surface's own copy is a useful live cache; your session log is canonical. Re-check the live
system before claiming publish, send, schedule, delete, approval, or completion.

## Step 3, Patterns (durable lessons)

Read every file in `patterns/active/` (shared path when the cutover is on). Budgeted to **a small card count and a small directory total** (per-card brevity is a guideline, the directory total is the cap). `patterns/reference/` is
the on-demand tier: pull a card from it when its topic comes up, not at session start. If `patterns/active/` is
empty, note it and continue.

## Step 4, Wiki and playbooks (ON DEMAND, not at session start)

Do NOT read the wiki indexes as part of loading. Per the house rule you consult the wiki when you need background you do not
have: read `wiki/index.md`, then the relevant `wiki/<topic>/index.md`, then the page. Cues: "what do we know about
X", "have we discussed Y", "prior thinking on Z", any question about the owner's domain knowledge. If no page exists for a
topic you need, build one before relying on it.

Playbooks work the same way: `agents/<agent>/playbooks/` is loaded per-playbook when the matching workflow runs, and
is not enumerated at session start.

## Step 5, Context brief (silent)

Absorb the context; do not dump it. Before starting work, confirm your approach matches what is already built: check
memory for existing conventions and decisions rather than inventing new ones.

Reply with one line: `memory loaded: <3 key facts you're carrying>` plus `shared memory: <source>`. This visible
receipt is what catches silent skips.

If the owner asks what you remember, give the full brief then: agent, last session, freshness, corrections count, vault
symlink status, active projects, open items, patterns loaded.

---

## Staleness

| Condition | Action |
|---|---|
| last-session > 7 days | Flag stale; treat stored facts as hypotheses (the house rule) |
| last-session > 30 days | Flag very stale; suggest a consolidator run + memory compaction |
| No session-index and no session logs | "Fresh start", no episodic context |
| Unresolved corrections exist | Flag the count; check before generating any output type corrected before |
| context.md over its ~8K budget | Flag it; fix at wrap-up by moving narrative to session-logs. Fix only YOUR OWN agent's file |

## Rules

- Step 0 may create symlinks; every other step is read-only.
- Silent by default. Absorb, do not dump.
- A missing or unreadable file is a note, not a failure. Continue.
- Use the correct agent directory; this skill is not claude-code-specific.
- **Never skip this skill.** Skipping session start has cost a whole re-done session.
