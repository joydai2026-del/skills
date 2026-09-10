---
name: hygiene
description: >-
  Monthly environment hygiene sweep for this Mac (disk, vault, Claude config, packages): stale projects, orphaned files, vault drift, skill rot. ALWAYS presents findings before deleting anything. Use when: "run hygiene", "monthly cleanup", "environment audit", "disk cleanup", "clean up my machine", "vault hygiene", "first Monday". PROACTIVE on the first Monday of each month, or when the owner mentions low disk space or vault drift.
version: 1.1.0
---

# Hygiene, Monthly Environment Sweep

Comprehensive hygiene for this Mac. Four scopes: **Disk**, **Vault**, **Claude**, **Packages**.

**Default mode: REVIEW-ONLY.** Present findings in tiered format (Safe / Verify / Keep) with counts, sizes, and a clear summary. Wait for the owner's explicit approval before any destructive action. When deleting, use `mv ~/.Trash/<dated-folder>/` (reversible 30 days), never `rm -rf`.

## Output Targets

1. **Chat**: concise tiered report (Safe to delete / Needs verification / Keep)
2. **Vault log**: `<maintenance-log>/YYYY-MM.md` (created each run with full findings, decisions, actions taken)

## Hard Rules (Never Skip)

1. **No `rm -rf`.** Every candidate deletion moves to `~/.Trash/hygiene-YYYY-MM-DD-HHMMSS/` for 30-day reversibility.
2. **Always ask before deleting.** Even "obvious" junk. The owner has been burned by aggressive cleanup before.
3. **Check external dependencies before classifying any folder as deletable:**
   - `which <command>`, is it on PATH?
   - `readlink <path>`, is it the target of a symlink elsewhere?
   - `grep -r "<path>" ~/.zshrc ~/.zshenv ~/.bashrc ~/.config/`, referenced in shell config?
   - `launchctl list | grep <name>`, loaded as launchd agent?
   - Known near-miss: a directory whose NAME reads like a stale project can be an ACTIVE SDK that something on PATH resolves into. Always check PATH first.
4. **Git remote verification.** Before proposing to delete any repo, check `git remote -v` in the repo. If no remote or remote doesn't exist on GitHub, explicitly warn that deletion = permanent data loss.
5. **No blanket gitignore rules for notes-sync conflicts.** See "notes-sync conflict triage" below.
6. **Tiered output** (never mix tiers):
   - **Safe to delete**: byte-identical duplicates, confirmed-retired projects with remote backup, build artifacts (node_modules/dist/target), byte-identical sync-conflict siblings.
   - **Needs verification**: divergent dups, unfamiliar folders, large folders with unclear purpose, projects without git remote.
   - **Keep**: active projects, SDK installs, folders referenced from PATH/shell/launchd/vault registry.

## Procedure

### Step 0: Create the month's vault log

```bash
MONTH=$(date +%Y-%m)
LOG="<HOME>/Documents/<NOTES_ROOT>/<maintenance-log>/${MONTH}.md"
```

If `$LOG` already exists (re-running same month), append a new dated section; don't overwrite.

### Step 1: Disk scope

Find large folders and candidate-retirements.

```bash
du -sh ~/Documents/GitHub/*/ ~/projects/*/ ~/dev/*/ ~/*/ 2>/dev/null | sort -rh | head -30
```

For each candidate folder:
- Check against vault project registry (`<HOME>/Documents/<NOTES_ROOT>/CLAUDE.md`).
- Check `git remote -v` if it's a repo.
- Check PATH/shell/launchd (per Hard Rules #3).

Cross-reference your own retired-projects list, if you keep one: those folders are already on the never-suggest-reviving list.

Flag for Trash: `~/.Trash/` items older than 30 days (macOS auto-empties but sometimes stalls).

Also check:
- `~/Downloads/` files older than 30 days
- `~/Desktop/` clutter (>10 loose files at root)
- Xcode derived data: `du -sh ~/Library/Developer/Xcode/DerivedData/ 2>/dev/null`
- Homebrew cache: `du -sh $(brew --cache) 2>/dev/null`
- **Docker disk image** (frequently the single biggest hog, and easy to miss): `du -sh ~/Library/Containers/com.docker.docker/Data/vms/0/data/Docker.raw 2>/dev/null`. It is a sparse file: logical size is huge, real on-disk size = `du`. `docker system prune` does NOT shrink it on disk. Reclaim by quitting Docker Desktop then deleting the raw (rebuilds empty on next launch) OR Docker Desktop -> Troubleshoot -> Clean/Purge. Check `docker ps -a` / `docker volume ls` for real data first (if the engine is down you get socket-missing errors, which means nothing is running and it is safe to reset).
- **App container / data hogs**: `du -sh ~/Library/Containers/* ~/Library/Application\ Support/* 2>/dev/null | sort -rh | head`, container directories, browser profiles, editor caches, and messaging-app attachment stores are commonly multi-GB.
- **Photos myth-check** (before EVER suggesting photo deletion): `du -sh ~/Pictures`. If ~0B, the Photos library is iCloud-offloaded (Optimize Mac Storage) and deleting frees NOTHING while risking the cloud copies. Always `du`-verify a category is actually local before recommending it.

### Step 2: Vault scope

Run `/vault-health` (already-installed skill) first to catch drift. Then specifically:

**a) project registry freshness**, read [the agent config file](<HOME>/Documents/<NOTES_ROOT>/CLAUDE.md) project registry. For each row:
- If Local Path is `—`, skip (already legacy).
- Otherwise, `test -d "$path"`. If path doesn't exist → registry drift, propose row update.

**b) Agent Registry freshness**, same, for the agent table. Archived agents should have `(archived → path)` annotation.

**c) Vault folder consistency**, any `projects/<name>/` without a registry row? Any registry row without a folder?

**d) Inbox backlog**, `ls <HOME>/Documents/<NOTES_ROOT>/inbox/ | wc -l`. If >10, suggest an inbox triage pass.

**e) Notes-sync conflict triage**, find ` 2.md` / ` 3.md` files:

```bash
fd -t f ' [0-9]\.md$' <HOME>/Documents/<NOTES_ROOT>/ 2>/dev/null
```

For each match, compare against the base file:
- **Byte-identical (sha256 match)**: Safe to delete (classic Sync round-trip artifact).
- **Dup is proper subset of base** (`diff` shows only `<` lines, no `>` lines): Safe to delete.
- **Divergent**: Needs verification. Before recommending delete, check if the dup's unique content might already live in a canonical artifact elsewhere (e.g. for a given project, a dated research log or a decisions log may already be the canonical copy). Dump unique-to-dup lines into a review section of the vault log.

**Do NOT** add a blanket `* 2.md` gitignore rule, it hides real conflicts. Use triage instead.

**f) Legacy folder check**, `legacy/projects/` should have `context.md` stubs for each retired project. Flag gaps.

### Step 3: Claude config scope

**a) Skills health**, run the Step 5 block from `/vault-health` (skills trigger audit, usage report).

**b) Stale agent memory**, any `~/.claude/projects/*/memory/*.md` with `description` pointing to retired/moved paths?

**c) Plans cleanup**, `ls ~/.claude/plans/`, any plan files with status `shipped` or `abandoned` older than 60 days can be archived into `~/.claude/plans/archive/`.

**d) Worktrees**, `ls -la <HOME>/Documents/<NOTES_ROOT>/.claude/worktrees/ 2>/dev/null`. Merged worktrees (branch already on main) are deletable via `git worktree remove`.

**e) MCP registry**, `cat ~/.claude/settings.json` (or wherever MCPs live). Flag any MCP entries that the owner has noted as retired or unauthenticated.

**f) Agent-rules audit**, two checks on whatever file holds your agent's standing rules:

1. **Budget**: it is loaded in full every session, so estimate its token cost (count CJK
   characters at roughly one token each and the rest at roughly four characters per token)
   and compare it against the budget you set. Over budget is a bug: propose which rule to
   compress or archive, never silently trim.
2. **Rule sunset review**: for each rule marked mandatory, look for a firing in the last six
   months: a dated incident cited in the rule itself, a matching entry in your corrections
   log or session index, or the owner restating it. List rules with NO observed firing under
   a RULES section of the report, each with a proposal to move it to a reference tier with a
   one-line pointer left behind. **Review-only like everything else in this skill: the owner
   decides, nothing moves automatically.**

### Step 4: Package managers scope

Do **not** auto-update (can break things). Just report what's outdated so the owner can choose.

```bash
brew outdated
brew cleanup --dry-run   # list what `brew cleanup` would remove
npm outdated -g 2>&1 | head -20   # global npm packages
du -sh $(npm root -g) 2>/dev/null   # size of global node_modules
```

Also:
- Orphaned homebrew deps: `brew autoremove --dry-run`
- Uninstalled apps leaving caches: `ls ~/Library/Application\ Support/ ~/Library/Caches/ | head -40`, only flag clearly-uninstalled apps, never active ones.

### Step 5: Compose report

Report template (chat + vault log both use this structure):

```
Hygiene Sweep — YYYY-MM-DD
===========================

SAFE TO DELETE (N items, ~X GB)
  - <item> — <size> — <reason>
  ...

NEEDS VERIFICATION (N items, ~X GB)
  - <item> — <size> — <why unsure + what to check>
  ...

KEEP (flagged but verified active)
  - <item> — <why keep>
  ...

VAULT DRIFT
  - Registry row <X> points to deleted path <Y>
  - Folder <Z> exists but no registry row
  ...

SKILLS & CONFIG
  - <findings>

PACKAGES
  - brew: N outdated, ~X MB cleanup possible
  - npm global: N outdated
  ...

Summary: approve Safe tier? Discuss Verification tier before any action?
```

### Step 6: On approval, execute

For each approved delete:

```bash
TRASH="$HOME/.Trash/hygiene-$(date +%Y-%m-%d-%H%M%S)"
mkdir -p "$TRASH"
mv <item> "$TRASH/"
```

Record each action in the vault log under "Actions Taken" with timestamp + size freed.

After execution, re-verify:
- Does a canary command you know is on PATH still resolve? (proves PATH not broken)
- Vault symlinks still resolve?
- Git status of any touched repo still clean?

### Step 7: Finalize vault log

Append to `<maintenance-log>/YYYY-MM.md`:
- Total space freed
- Items kept for next month's review
- Any process improvements for the skill itself (feed back to SKILL.md)

## Rules

- Read the vault registry BEFORE classifying any project folder.
- Check PATH/symlinks/launchd BEFORE classifying any root-level folder.
- Trash, never `rm -rf`.
- No blanket gitignores for notes-sync conflicts.
- Default to "Needs verification" when uncertain. "Safe" tier requires **all four** checks passed: not on PATH, not symlinked, not in shell config, not in launchd.
- If a repo has no git remote AND contains uncommitted work, move to "Keep" tier regardless of apparent staleness.
- Append to the monthly log, never overwrite.

- **Probe a daemon with a command that actually talks to the daemon.** A client-side `--format` query prints
  its format string whether or not the server is up. On a full disk those probes can HANG rather than error,
  so run them bounded or in the background and never let one stall the sweep.
- **A shell loop that reports zero may never have run.** In zsh an unquoted `$VAR` is not word-split, so a
  sweep loop can process nothing while the echoed counts look right. Before reporting ANY zero-findings
  result, prove the scanner ran: check the binary is on PATH, or test it against a known positive. A missing
  tool writes its error to stderr and leaves a clean-looking `0` in the report.
- **Get the tiering reviewed before a human sees it.** A second reviewer routinely upgrades classifications.
  Durable rules worth keeping: a repo with NO remote and recent writes is KEEP even if the project is
  stopped; a Trash-move frees nothing until the Trash is emptied, which is the owner's call and never the
  agent's, and on APFS local snapshots pin that space for about a day afterwards; a package-manager cleanup
  that deletes directly instead of moving to the Trash needs its own approval line.
- **`du` lies about cloned files; judge the win by `df`.** Some large directories share blocks with a live
  application, so moving them frees roughly nothing. Report free space before and after, never the size of
  what moved.
- **Check what is holding a directory before Safe-tiering it.** A dependency directory can pass every PATH,
  launchd, process and remote check and still be held open by an editor or indexer (`lsof +D <dir>`). On any
  "in use" report, restore rather than force.
- **Name-based staleness lies.** A directory named like an old iteration can hold yesterday's work. Prove it
  cheaply: list by modification time for recent writes, then checksum-overlap it against the delivered
  output. No recent writes AND no overlap means deletable; recent writes mean it is live.
- **Split destructive-adjacent shell into separate steps.** Write a manifest first, review it, then move the
  listed paths in a plain read-loop. And read the actual lines of any sync output before claiming a
  difference: warnings land on stdout and inflate a line count.
- **Root-owned items cannot be Trash-moved by an agent.** Hand a human ONE elevated move command covering all
  of them, then verify with `ls -d` that they left.
