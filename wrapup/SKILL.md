---
name: wrapup
description: >
  End-of-session protocol. Write session log, update context.md, corrections log,
  pattern promotion, plan archival, vault overview sync, skill self-improvement, last_used
  tracking, Claude/Codex skills sync to GitHub, an unpushed-work sweep across every git
  worktree, and next-session prompt generation.
  Use when: "wrap up", "wrapup", "session end", "end session", "save session",
  "close session", "log this session", "update memory", "finish", "done for now",
  "close this out".
  PROACTIVE TRIGGER (MANDATORY): Must run at the end of every substantive session.
write_scope: self-only
version: 4.2.0
author: the repository owner
contributors: []
---

# /wrapup, Session Wrap-Up Skill

## Instructions

This skill wraps the existing agent wrapup playbook for Claude Code, Codex, and other agent platforms. When invoked, read and execute your own long-form wrap-up playbook, the template file where you keep the full step list.

That playbook contains the complete step-by-step instructions for all 13 mandatory outputs:

1. Session log (`session-logs/YYYY-MM-DD.md`)
2. Session index entry (`session-index.md`)
3. Context update (`context.md` / `memory.md`)
4. Corrections log (`corrections.md`)
5. Decision files (`decisions/`)
6. Pattern promotion + confidence upgrade + identity sync
7. Plan auto-archive
8. Vault project overview sync
9. Skill self-improvement
10. Usage tracking
11. Skills sync to your remote
12. Unpushed-work sweep across every git worktree
13. Next-session prompt generation

## Key Rules

- **Gotcha: the Bash working directory PERSISTS between tool calls.** Several playbook steps `cd <HOME>/Documents/<NOTES_ROOT>` (the memory dual-writes, the consolidation run). After those, you are still in the vault, so a later PROJECT-relative path silently breaks, `ls <NOTES_LINK>/` and `docs/plans/*.md` return "No such file or directory" and can look like a missing symlink or a deleted plan. Use ABSOLUTE paths for every project file after the first vault `cd`, or keep the `cd` inside the same command as the thing it serves. (Same root cause as the earlier "cwd reset put a git commit on the wrong repo" incident.)

- **All outputs are mandatory**, no exceptions.

- **UNPUSHED-WORK SWEEP: run it EVERY wrap-up. It is not optional, and `git status` does not cover it.**
  `git status` reports ONE checkout. A busy project carries several worktrees plus branches nobody has open,
  and neither is visible there, so finished work can sit on one machine only, on a branch with no remote at
  all, while every wrap-up honestly reports a clean tree. Sweep every repo the session touched:
  ```bash
  # read-only: expand every worktree, then list branches with no upstream or with unpushed commits
  for wt in $(git -C "<repo>" worktree list --porcelain | awk '/^worktree /{print $2}'); do
    git -C "$wt" for-each-ref --format='%(refname:short) %(upstream:short) %(upstream:track)' refs/heads
  done
  ```
  A branch with an EMPTY upstream column has never reached the remote; that is the loudest line. Publish the
  safe ones (never force, never the trunk branch, never commits that are not yours), report the counts, and
  hand a human anything you cannot publish safely. Never resolve it with a force-push.

- **An append writes TWO copies; editing only one breaks reconcile.**
  The store's append writes the shared copy AND the per-agent mirror. If you then revise the file, editing
  only one side leaves the other stale and the reconcile check reports a content mismatch. Two safe ways to
  revise after an append: edit your content file and re-append with the SAME event id (append is idempotent),
  or edit one side and copy it over the other. Writing a file straight into the shared store with no append at
  all leaves an orphan belonging to no mirror. **Always finish a wrap-up by running the store's reconcile
  check and confirming it comes back clean.**
- **Shared memory layer:** when the shared store is switched on, dual-write the consolidated kinds
  (corrections, session index, patterns, session logs, decisions) to BOTH your own mirror and the shared
  store, always through the store's append command. Never raw-append to the mirror files while the switch is
  on: that is the baseline-drift vector. Per-agent working memory, identity, and learning journals stay
  per-agent. If the switch is off, write per-agent as before.
- **Consolidator (once per wrap-up, AFTER the dual-write):** consolidate the freshly written store, so a
  correction that has recurred enough times is promoted to a durable pattern card, stamped back onto the
  corrections it came from, and any contradiction is routed to a conflicts file. Run it with archive-pruning
  ON. It holds the store lock and self-verifies, so report its verdict and its counts. The prune proves
  conservation before it archives anything and aborts the whole run if that proof fails, which is why no
  separate dry run is needed. If it reports a failed conservation check or an aborted verdict, stop and
  investigate; do not retry blind.
- If the session log file already exists, **append** with a `---` separator.
- **Working memory has a TOKEN budget, not a line count.**
  A line count is the wrong unit: one line can hold an entire session narrative, so a file can pass a line
  rule while growing far past what it costs to load. A byte cap is wrong too, because the cost depends on the
  language mix: CJK text costs roughly one token per one to one-and-a-half characters where English costs
  about four characters per token, so a mostly-Chinese file busts a token budget long before it busts a byte
  cap. Estimate it every wrap-up as `(CJK characters / 1.3) + (other characters / 4)` and have the check EXIT
  NON-ZERO when it is over, so the budget is a gate rather than a number to squint at.
  Over budget means fix it in THIS wrap-up, not later: copy the current file to a dated archive copy (never
  delete it, for older sessions it may be the only surviving record), then rewrite it as one short block per
  ACTIVE project (status, next action, the gotcha that would cost a session) plus the durable workflow rules.
  Full narratives belong in the session logs.
  **Only ever trim YOUR OWN agent's working memory.** It is per-agent, and rewriting another agent's is the
  silent cross-agent overwrite a shared memory layer exists to prevent.
- Each agent writes only inside its own `agents/<agent>/` directory. Do not mix these files.
- Treat "finish" and equivalent end-of-session phrasing as an instruction to run `/wrapup`.
- Do NOT confirm until all outputs are written.

- **A `git push origin main` that keeps failing usually means the checkout is NOT on `main`.**
  A long-lived repo often sits on a feature branch. `git add -A && git commit && git push` then commits onto
  THAT branch, and `git push origin main` pushes the stale LOCAL `main` ref, which really is behind the
  remote, so git's "your branch tip is behind its remote counterpart" is accurate but points at the wrong
  branch, and a fetch/reset/retry loop never converges. **Check `git symbolic-ref --short HEAD` FIRST.** If it
  is not `main`, push the commit explicitly with `git push origin HEAD:refs/heads/main`.
  **And do NOT `git reset --hard origin/main` to "sync": that silently moves the checked-out FEATURE branch
  off its own history** and is recoverable only through the reflog. Restore with `git branch -f main
  <pushed-sha>` then `git reset --hard <pre-session-sha>` read out of `git reflog`.
  Also: `git push ... | tail -3 && echo PUSHED` reports success even when the push failed, because the exit
  code comes from `tail`. Never pipe a push through anything you then test with `&&`.

## Reference

If you want the full step list kept separately, save it as your own template file and point at it from here.

Durable lessons worth carrying into any wrap-up, with no incident history attached:

- A shell working directory persists between tool calls. After the first `cd` into your notes tree, use
  absolute paths for every project file, or keep the `cd` inside the same command as the thing it serves.
- Stage the EXACT files this session wrote, then run `git diff --cached --name-only` and confirm every
  entry is yours. A shared multi-session repo will otherwise sweep another session's in-progress files
  into your commit, and a background hook may have staged them before you started.
- A working-memory file has a token budget, not a line budget. Re-measure after every edit, compress your
  own block first, and report the exact number rather than cutting another session's live state.
- Prove a memory write positively: check that each identifier the write returned appears in BOTH copies.
  Do not trust an aggregate ok flag, and do not prove it with a recursive grep over a large store.
- A verification that reports EVERYTHING broken is usually the verification, not the system. Re-read the
  command and look for an error the summary formatting swallowed.
- Never pipe a push (or any command whose exit code you then test) through another command: the exit code
  you get back belongs to the last command in the pipe.
- A deliverable written into a directory that is a symlink into another repo belongs to THAT repo's commit.
  Check where the directory actually points before assuming a file is missing or unstageable.
- If a long-running maintenance job blocks with no lock holder, bound it and continue. Confirm the bound
  actually exists on your platform: a missing `timeout` binary silently turns the bound into no bound.
