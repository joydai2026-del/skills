---
name: ai-done
description: |
  Defines and verifies "done" for AI / LLM / agent features, where output is
  probabilistic rather than deterministic. An AI feature is done when it is
  CALIBRATED across 5 layers (deterministic floor, distributional quality,
  failure-triage playbook, tripwires + rehearsed rollback, post-ship eval loop),
  NOT when "all tests pass". This is a thin orchestrator that runs AFTER the
  deterministic checklist exists: it delegates the pass/fail checklist to
  /success-criteria and the eval suite to /eval-harness, then adds the failure,
  rollback, and monitoring layers those skills do not cover. It earns its own
  skill (rather than being folded into /success-criteria) because it delegates to
  TWO skills, adds three operational layers, and slots into /planning-pipeline as
  its own step.
  Use when: "is this AI feature done", "ready to ship the model/agent", "calibrate
  this AI feature", "AI done bar", "how do I know this model/agent is ready",
  "done bar for a probabilistic feature", "ship checklist for an LLM feature".
  For NON-AI / purely deterministic features, use /success-criteria instead.
  RELATIONSHIP TO /success-criteria (sequential, NOT competing): /success-criteria
  owns the generic deterministic "definition of done". When a feature has a
  probabilistic surface, /success-criteria (or /planning-pipeline) hands off to
  /ai-done AFTER the deterministic checklist is generated. Do not race it on the
  bare word "done".
allowed-tools:
  - Read
  - Write
  - Edit
  - Bash
  - Grep
  - Glob
  - Skill
last_used: ""
gotchas_count: 0
author: jj
contributors: []
---

# AI Done: Definition of Done for AI Features

Traditional software is a vending machine: the same input gives the same output, and "all tests pass" means done. AI features break that. The same prompt produces different outputs across users, sessions, model updates, and contexts your QA never imagined. What you shipped is not a fixed object, it is a distribution of behaviors. So "done" cannot be a checkbox.

**An AI feature is done when it is calibrated:** you have decided the variance you will accept, planned for the failure modes, rehearsed the rollback, and wired the loop that catches the surprises that show up after launch.

> "If 'done' was the language of a build culture, 'calibrated' is the language of a learning culture." (Jeff Gothelf, "What 'done' means when you're shipping AI features", https://jeffgothelf.com/blog/what-done-means-when-youre-shipping-ai-features/ , retrieved 2026-06-02)

This skill is a **thin orchestrator**. It does NOT re-implement what you already have:

- Deterministic pass/fail checklist: it calls **/success-criteria** (then /qa-runner).
- Probabilistic eval suite + grading: it calls **/eval-harness**.

It adds the three layers neither of those covers (failure-triage playbook, tripwires + a rehearsed rollback, post-ship eval loop), then stamps the global QA bar. If you find yourself hand-writing pass/fail checks or eval graders inside this skill, stop and call the owning skill. Staying thin is the point.

## When to use (and when NOT to)

USE when the feature has any probabilistic / AI surface: an LLM call, model inference, an agent loop, a recommender, generated text / image / audio, or a classifier whose output a user sees.

DO NOT use for purely deterministic features (CRUD, auth, billing, navigation, static UI). For those, /success-criteria plus the standard QA bar (static analysis + tests + device) is the whole story. Step 0 below will bounce a non-AI feature there.

## The AI Done Bar (the global definition)

An AI feature is done only when ALL FIVE layers are green:

| Layer | What it means | Owner skill / source |
|---|---|---|
| L1 Deterministic floor | The non-AI parts pass hard pass/fail checks (auth, billing, nav, data integrity) plus the global QA base (static analysis, tests, device/real-env). | /success-criteria + /qa-runner (checklist); QA base verified directly |
| L2 Distributional quality | The AI parts have quality bands written as RANGES (the spread of outputs you will accept), an eval suite, and a passing baseline. The tail (the share that misses the bar) fails gracefully, not embarrassingly. | /eval-harness |
| L3 Failure-triage playbook | For each failure class, a named owner (or, solo, a pre-decided action) + first action + escalation. Done means whoever is downstream knows what to do when it misbehaves. | this skill (the gap) |
| L4 Tripwires + rehearsed rollback | At least one metric + threshold + alarm destination, AND a written kill switch that has been RUN once (with a real artifact as evidence). | this skill (the gap) |
| L5 Post-ship eval loop | Production outputs sampled back into the eval suite, with a named reviewer + cadence. Outputs are not outcomes. | this skill (the loop); /eval-harness provides the suite it feeds |

Half-green is not done. Green L1/L2 with a red L3, L4, or L5 is the most common and most dangerous "looks done, isn't" state.

### Solo or small team (read this before L3 to L5)

JJ usually ships AI products as a one-person team on Modal / Supabase. L3 to L5 still apply, but they degrade honestly. Do NOT write "JJ / JJ / JJ / JJ" and call it triage. Instead:

- **L3 owner:** when there is only one person, the cell records the pre-decided ACTION and the escalation trigger, not a name. Example: "If Safety/PR fires: hit the kill switch immediately, do not triage live. If model-quality: log it, batch-fix next session."
- **L4 "alarm destination":** for a solo dev, "paged" legitimately means a webhook to yourself (Telegram / Slack / email). Name the actual channel. A tripwire metric with no destination at all is theater.
- **L5 "reviewer + cadence":** one person is fine; pin the cadence (for example "JJ, Friday, 15 sampled outputs + every flagged one") so it is a commitment, not a vibe.

The point of these layers for a solo founder is not bureaucracy. It is a pre-decided plan so that when the model embarrasses you, you are not improvising in front of a customer.

## Procedure

### Step 0: Scope gate (AI surface check)

Identify whether the feature has a probabilistic surface (LLM call, inference, agent, generation, recommendation, user-facing classification).

- If NO: stop. Tell the user: "No AI surface detected. Use /success-criteria plus the standard QA bar (static analysis + tests + device). /ai-done adds nothing here."
- If YES: continue. Note which surfaces are AI and which are not (you split them in Step 1).

### Step 1: Split into two lists (deterministic vs probabilistic)

An AI feature is part vending machine, part distribution. List both:

- **Deterministic surfaces:** auth, billing, navigation, persistence, API contracts, anything with one correct answer.
- **Probabilistic surfaces:** the model's outputs, agent decisions, generated content, rankings.

Write both lists into the calibration doc ("Surface split" section of the template).

### Step 2: L1 deterministic floor (delegate to /success-criteria)

Two distinct things, do not conflate them:

1. Invoke **/success-criteria** on the deterministic list to generate or extend the machine-checkable checklist (it writes qa-checklist.yaml or the project's own convention; hand off to /qa-runner). Record the checklist path in the calibration doc. Do NOT hand-write these checks here.
2. Verify the global QA base DIRECTLY: static analysis clean, test suite green, device / real-env run where applicable. /success-criteria only GENERATES the checklist, it does not itself run static analysis or device tests, so confirm these yourself (or via /verify).

### Step 3: L2 distributional quality (delegate to /eval-harness)

Invoke **/eval-harness** for the probabilistic list. Define acceptance as RANGES, not assertions:

- Good shape: "For at least 80% of inputs in category X, output meets quality bar Y (grader Z). For the remaining 20% or fewer, the failure mode is <degraded but acceptable behavior>, never <embarrassing or harmful behavior>." (The 80/20 split is an example; pick the band the feature actually needs.)

Capture: the quality bands per category, the eval-suite location, the graders (code / model / human), and the passing baseline. Record the eval-suite path + current baseline in the calibration doc. Do NOT hand-write graders here.

### Step 4: L3 failure-triage playbook (the gap, fill it here)

For EACH failure class, fill the triage table: owner, first action, escalation. On a real team the owner is a named person or role; solo, it is the pre-decided action (see the "Solo or small team" note above). Minimum classes:

| Failure class | Example | Owner / pre-decided action | First action | Escalate to |
|---|---|---|---|---|
| Model quality | wrong / low-quality / irrelevant output | | | |
| UX | user cannot tell it failed, or cannot recover | | | |
| Content | off-brand, low-quality, or confusing generation | | | |
| Safety / PR | harmful, biased, embarrassing, or legally risky output | | | |

A failure class with neither a named owner nor a pre-decided action is not done.

### Step 5: L4 tripwires + rehearsed rollback (the gap, fill it here)

1. Define at least one tripwire: a metric (error rate, complaint frequency, hallucination / refusal rate, latency, cost), a threshold that means "failing," and an alarm DESTINATION (a real channel: PagerDuty, a Telegram / Slack / email webhook, a dashboard alert). A tripwire with no destination is theater.
2. Write the rollback: the exact steps to disable or revert the AI surface (flip the feature flag off, revert to the previous model/prompt, or fall back to the deterministic path).
3. REHEARSE it and capture a REAL artifact: actually run the rollback once (staging or a controlled prod drill) and paste the concrete evidence into the calibration doc, the actual command + its real output (or a log line, or a screenshot path), plus the environment and timestamp. A prose claim like "flipped the flag, it worked" is NOT acceptable (it violates the real-data-only rule and is unverifiable). The moment you find out your rollback will not work is the moment you most need it. An un-rehearsed rollback does NOT count toward done.

### Step 6: L5 post-ship eval loop (the gap, fill it here)

This is this skill's layer, /eval-harness does not do post-ship sampling. Wire a concrete loop:

1. **Capture:** decide where production outputs (or a sample of them) are logged or stored (the app's request log, a Supabase table, a Modal volume, an LLM-observability tool).
2. **Sample:** pick how a sample is drawn for review (for example N random per period + every tripwire-flagged output).
3. **Feed back:** when a sampled output is a failure, it becomes a new case in the /eval-harness suite, so the regression set grows with each real-world failure mode.
4. **Reviewer + cadence:** assign who reviews and how often (solo is fine; pin the day and the sample size).
5. **Outcome metric:** name the metric that proves customers actually succeeded, not just that the feature shipped (outputs are not outcomes). This is the section 3.9 customer-need check applied after launch, not a separate gate.

### Step 7: Emit the Done Calibration doc + stamp the global bar

Write a one-page calibration doc using `references/ai-done-calibration-template.md`. Choose the path deterministically (do not hardcode):

1. If the project already has a qa-checklist (from /success-criteria), put the calibration doc in the SAME directory so L1 and the calibration sit together.
2. Else if the project has a `docs/` directory, use `docs/ai-done-calibration-<feature>.md`.
3. Else project root: `ai-done-calibration-<feature>.md`.

Do not write it into `.vault/` (that syncs to Obsidian and is not the home for a per-feature ship artifact) unless the project has no `docs/` and no checklist dir.

Link the qa-checklist (L1) and the eval suite (L2); inline L3, L4, L5. Then, if `~/.claude/CLAUDE.md` (vault `system/user.md`) section 3.13 carries an AI-features clause pointing here (item 4), confirm this calibration satisfies it. If section 3.13 lists only the deterministic checks with no AI clause, the five-layer bar in THIS skill is the AI definition of done; note that in the doc and proceed.

### Step 8: Readiness summary (fail closed)

Print the 5-layer status table (green / red per layer). Rules:

- Done ONLY if all five are green.
- L4 is RED if the rehearsal evidence is a prose claim rather than a real artifact (command output, log line, or screenshot path). Do not mark L4 green on a promise.
- If any layer is red, list exactly what is missing and who (or what action) owns closing it.

Never report "done" on a partial bar (recurring failure pattern; see the global QA Completion Bar, section 3.13).

## Anti-patterns (do not ship on these)

- "All tests pass, so it's done." Tests cover L1 only. L2 through L5 are the AI part.
- Re-implementing /success-criteria or /eval-harness inside this skill. If you are hand-writing pass/fail checks or graders here, STOP and call the skill. This skill stays thin on purpose.
- Bands that describe only the happy 80% and ignore what the tail does.
- A tripwire with no alarm destination or no owner / pre-decided action.
- A rollback that is written but never run, OR "rehearsal evidence" that is a sentence instead of a real artifact.
- Treating "shipped" as "customer succeeded" (skipping L5 / the outcome metric).
- Running this on a deterministic feature (Step 0 should have bounced it to /success-criteria).

## Composes with

- **/planning-pipeline:** runs right after /success-criteria for any AI feature, so the AI-done plan is produced during planning, not bolted on at ship time.
- **/success-criteria** (L1, sequential handoff in, not a competitor), **/qa-runner** (L1 loop), **/eval-harness** (L2 suite + the suite L5 feeds), **/verify** (build / type / lint / test / security), **/investigate** (when a tripwire fires in production).

## References

- `references/ai-done-calibration-template.md`: the fill-in template for the per-feature calibration doc.
- Source thesis: Jeff Gothelf, "What 'done' means when you're shipping AI features" (https://jeffgothelf.com/blog/what-done-means-when-youre-shipping-ai-features/ , retrieved 2026-06-02). Done = a calibration of acceptable variance + planned failure modes + a rehearsed response, with monitoring that keeps learning after launch.

## Gotchas

<!-- Updated whenever Claude fails at something while using this skill -->
<!-- Format: date | what failed | why | fix/workaround -->

| Date | Issue | Root Cause | Status |
|------|-------|------------|--------|
| | | | |
