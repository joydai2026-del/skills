---
name: ship-it
description: >-
  Autonomous build-to-ready loop: secrets up front, success criteria first, then build, verify, QA the live surface. Use when: "ship it", "/ship-it", "build this autonomously", "build until it's ready for me to test", "self-driving build", "loop until the success criteria are met", "build it hands-off", "keep going until everything passes". NOT for deploying a finished PR (use your ship workflow), an existing checklist, or pure planning (/planning-pipeline).
allowed-tools:
  - Bash
  - Read
  - Write
  - Edit
  - Grep
  - Glob
  - Skill
  - Agent
  - AskUserQuestion
author: the repository owner
contributors: []
---

# Ship It: Autonomous Build-to-Ready Loop

You name the feature once. This self-drives until the work is provably ready for you to test, and it halts the instant it needs something only you can give.

The loop is the easy part. The VERIFIER is what makes it safe to run unattended. So this skill writes the verifier (machine-checkable success criteria) FIRST, then loops against it. That is why it cannot declare fake victory or spin forever.

## When to use / not use

- USE: "build X until it's ready for me to test", any hands-off build.
- NOT: deploying a finished PR (use your ship workflow); looping an existing `qa-checklist.yaml` (self-loop it here); planning a feature that isn't scoped yet (use `/planning-pipeline` first, then come here).

## What you type

`/ship-it <feature description>`  (optionally: `/ship-it <feature> using the plan at <path>`)

## The autonomous contract (what "hands-off" means here)

- I gather every human-only input UP FRONT in a pre-flight (Step 0.5), then run to the end without stopping for it. You answer one batch of questions at the start, then you next see me at the finish line.
- I do NOT ask you to confirm intermediate steps. I work until done.
- I do NOT trim the feature to dodge a hard part. If the architecture fights back, I fix the architecture or I surface it. Your locked end-state does not shrink (the house rule).
- I do NOT fake "done". Done = the gate is green AND the live-QA loop has driven the real surface until it actually works (the house rule), not one happy-path click.
- I only stop mid-run for a genuinely UNFORESEEABLE blocker the pre-flight could not have predicted. That is the rare exception, not the plan.
- Every loop is bounded. No never-exiting loop (the house rule).

## Procedure

### Step 0: Scope + branch

1. Restate the feature in one line so the target is explicit.
2. AI-surface check: does it have an LLM call, agent loop, generation, recommendation, or user-facing classifier? Record yes/no (drives Step 4).
3. Cut a branch (never work on main, the house rule): `git checkout main && git pull && git checkout -b feat/<slug>`. Verify the base actually carries the trunk this feature needs (`git ls-tree <base> <key-path>`) before the first edit.

### Step 0.5: Pre-flight, collect every human-only input NOW, not mid-run

Hands-off means you answer once, at the start, then walk away. So before the loop begins, predict everything only the owner can provide and ask for all of it in ONE batch. Never discover these one at a time mid-build.

1. Scan the plan + the codebase + the surfaces this will touch, and list every human-only input the build will plausibly need:
   - **Secrets / credentials:** API keys, tokens, deploy keys, DB URLs, webhook secrets.
   - **Access / accounts:** a login, an OAuth grant, a paid-tier account, a domain, a device.
   - **Human gates:** a 2FA step, a CAPTCHA, a real-device tap, any physical action.
   - **Spend / side-effect consent:** authorization to incur paid-API or deploy cost, and consent to fire real external side effects during the live loop (a real email / Telegram send, a write to a production DB, a post to a live channel).
   - **Real inputs + target:** real test fixtures / seed data / sample files the live-QA loop needs to exercise a flow (never fabricated, the house real-data rule), and which environment the live surface runs against (which backend project / app deployment / domain / branch).
   - **Taste defaults:** any "does this look right / is this band acceptable" call (handled per step 4 below).
2. Present the whole list at once (AskUserQuestion or a short checklist). For each item, the owner either provides it, points you at where it already lives, or says "skip / mock it". If the owner defers or mocks an input a feature genuinely needs, the live-surface check that depends on it is marked `blocked` with that reason in Step 5, never allowed to pass against the stub.
3. **Secrets are NEVER pasted into chat.** Ask the owner to place them in the project's `.env` / keychain (or confirm they are already there), then verify PRESENCE only, never printing the value (the house rule): the key NAME exists in `.env`, or `test -n "$VAR"` passes. You enter no passwords or keys yourself; that is the owner's to do.
4. **Taste defaults do not halt the loop later.** State the default you will use ("I will follow the existing design system", "I will target an 80% quality band"), proceed, and flag it in the final summary for the owner to override. The only taste calls that stay the owner's hard sign-offs are the AI quality band and the rollback approval (Step 4); capture the owner's preference on those here too, up front.
5. Block here until every foreseeable input is provided, located, or explicitly deferred. THEN start the loop and do not come back until Step 7 (or a true unforeseeable blocker).

### Step 1: Write the gate FIRST (`/success-criteria`)

Run `/success-criteria` on the plan (or, if none, on the feature description + the codebase) to produce `docs/qa-checklist.yaml`. Every requirement becomes an automated command or a code-audit check. THIS FILE IS THE STOP CONDITION.

Make sure at least one check is a LIVE-SURFACE check (the house rule): it exercises the real user path (hit the real URL, the real bot, click the real button), not only a unit test. A loop with no live check can go green while the real surface is dead.

### Step 2: The build loop (self-driving)

Loop until every check in `qa-checklist.yaml` is `passed` or provably `blocked`:

1. Pick the next failing or pending check.
2. Implement the minimal change to satisfy it (surgical, no speculative abstractions).
3. Re-run that check plus the build as a regression gate. If the build breaks, revert just that edit (`git checkout -- <file>` or `git stash`), never `git reset --hard` (it would nuke other in-progress work).
4. If a previously-passing check regressed, fix that before new work.
5. Repeat.

**Never game the gate (reward-hacking guard, adapted from a goal-loop contract).** Make a check pass by satisfying its INTENT, never by defeating it. Do NOT delete, skip, weaken, narrow, or `xfail` a test, loosen an assertion, or edit `qa-checklist.yaml` to lower the bar, just to turn a check green. If a check is genuinely wrong or over-specified, surface it as a WHAT question (do not silently rewrite it). A gate you gamed is a gate that will let the real bug reach the owner.

Self-loop here directly. That is the default and it is always safe. You may instead delegate the loop to a continuation-enforcing loop plugin, if your setup has one enabled. If you are unsure, self-loop.

**Bound the loop, either path:** if one check fails the same way 3 times, mark it `blocked` with the reason and move on. Never grind a single check forever, and never let the loop run unbounded (the house rule).

### Step 3: The verify gate (`/verify`)

Run `/verify` (build, type-check, lint, tests, security scan, diff review). Require a clean PASS (warnings are fine, failures are not). Fix and re-run until clean.

### Step 4: AI surface? Calibrate it (`/ai-done`)

If Step 0 marked an AI surface: run `/ai-done`. The feature is NOT done until all five layers are green. `/ai-done` runs `/eval-harness` for the distributional-quality layer, so the evals fire here.

HONEST CEILING: two of ai-done's layers are yours, not mine. The acceptable quality band (L2: what counts as good enough) and the rollback approval (L4) are WHAT decisions. Build everything else, run the evals, PROPOSE the band and the rollback, and list those two as pending sign-offs. Do not invent them.

If no AI surface: skip this step. A deterministic feature is done at Steps 1 to 3 plus the live-QA loop (Step 5).

### Step 5: Live-QA loop, drive the real surface until it actually works (the house rule)

One happy-path click is not proof. Real users find dead buttons, broken states, and output that makes no sense. So this is a LOOP, not a single run, against the actual surface a user touches (the real URL / bot / button / the real API the running app calls), on the same build the checks ran.

Loop until every live-surface check from the verifier passes on the REAL surface:

1. Drive the real surface with the project's QA browser tool (a fast headless browser driver for quick loops, a full end-to-end framework such as Playwright for deeper runs; pick by project, do not hardcode). For a non-web surface, exercise the real entry point a user hits (the bot chat, the CLI, the deployed API).
2. Walk the actual user flows the feature touches. Click the buttons, submit the forms, read the real output. Hunt for dead controls, error states, broken layout, and output that is wrong or nonsensical, not just "did it return 200".
3. Found a problem? Root-cause it (find the cause before you patch the symptom), fix it, and re-drive the surface. A live failure is a failing check, not a note for later.
4. Bound it (the house rule): if the same live issue resists a fix 3 times, mark it blocked with the reason and move on; cap total live iterations so it never spins forever.
5. The loop ends only when the live experience matches the goals: every live-surface check passes on the real thing, exercised end to end. State plainly what you verified live vs what is still assumed.

### Step 6: Unforeseeable-blocker fallback (rare, the pre-flight should have caught it)

The pre-flight (Step 0.5) front-loaded every input you could predict, so you should reach Step 7 without stopping. If, and ONLY if, you hit something genuinely unforeseeable that you cannot clear yourself, STOP. Do not fake past it, do not trim the feature. Hand over a short, exact list:

> "N of M checks green. Hit an unforeseen blocker: <what> needs <exactly what you need from the owner>. Give me that and I finish."

If this fires, the pre-flight missed something. Name it, so next run's Step 0.5 asks for it up front. Taste calls do NOT belong here: they were defaulted-and-flagged in Step 0.5, never halted on.

### Step 7: Ready-for-you summary

When the gate is green (or only the owner-blockers remain), stop and hand over five lines:

1. What got built (one line).
2. Gate status: X/Y checks green; if there were evals, the two-bucket result (hard gates + quality score with N and a Wilson CI), and flag that the accept/reject threshold on the quality band is still the owner's call (per Step 4).
3. What you verified LIVE vs what is still assumed.
4. The exact steps for the owner to TEST (the human acceptance run).
5. Anything pending the owner (secrets, sign-offs, the AI band/rollback approvals).

## Anti-patterns

- Asking the owner to confirm each step. The whole point is hands-off.
- Stopping mid-run to ask for a secret, login, or permission. Front-load every one of them in the Step 0.5 pre-flight.
- Treating the live check as one happy-path click. It is a loop until the real surface actually works (the house rule, seen 7+ times).
- Declaring done on green tests with no live-QA loop (the house rule).
- Trimming a user-facing surface to dodge a hard blocker (the house rule). Fix the architecture or surface it.
- A never-exiting loop (the house rule). Bound every loop; mark stuck checks blocked with a reason.
- Trusting skills to auto-trigger. This skill NAMES `/success-criteria`, `/verify`, and `/ai-done` so they actually fire.
- Gaming the stop condition: weakening, deleting, or narrowing tests, or editing the checklist to force green. The gate is satisfied honestly or it is surfaced, never defeated.

## Composes with

`/success-criteria` (writes the gate) then the checklist loop (self-loop, or a continuation-enforcing loop plugin when one is enabled) then `/verify` (build gate) then `/ai-done` (AI calibration, which runs `/eval-harness`).
