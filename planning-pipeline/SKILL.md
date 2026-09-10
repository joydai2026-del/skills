---
name: planning-pipeline
version: 2.0.0
description: >-
  Product planning gauntlet for NEW PRODUCTS and WEEK-SCALE multi-stage builds: five review lenses (idea, scope, engineering, design, success criteria) in ONE pass. Suggest when starting a new product, describing a multi-week build, or committing to an expensive-to-change architecture. DO NOT trigger for a single feature in an existing product, a bug fix, a refactor, a config change, or prototyping.
allowed-tools:
  - Bash
  - Read
  - Write
  - Edit
  - Grep
  - Glob
  - AskUserQuestion
  - Agent
---

# Planning Pipeline, Five Lenses, One Pass

## Purpose
Force the expensive questions (is this worth building, is the scope right, will the architecture hold, what does the
user actually see, how do we know it worked) BEFORE code exists, for work where being wrong is expensive.

## When this fires vs. when it does not

| Situation | Path |
|---|---|
| New product, new project, new revenue surface | **This pipeline** |
| Multi-week build with staged milestones | **This pipeline** |
| Architecture decision that is costly to reverse | **This pipeline** |
| A feature inside an existing product | Built-in Plan mode + 1 Codex adversarial round |
| Bug fix | a root-cause investigation pass |
| Refactor, config, typo, <10 lines | Just do it |
| Exploratory prototyping | Just do it, no gate |

When in doubt, ask the owner in one line which path they want. Do not run the full pipeline by default.

## Execution: ONE subagent, five lenses, one deliverable

Do NOT invoke five separate review skills serially. That was the v1 design and it cost five standalone review docs
of context for one plan. Spawn **one Plan subagent** whose
prompt runs all five lenses and returns a single structured deliverable with drop-in blocks for the plan file.

Subagent prompt skeleton:

```
Review <plan file or brief> through five lenses. Return ONE structured deliverable with a
drop-in markdown block per section, not five essays.

§A IDEA VALIDATION — Who desperately needs this today? What do they do instead right now?
   What is the narrowest wedge that would still be worth shipping? Is there a smaller
   version that suffices? (If the honest answer is "no real user needs this yet", say so
   loudly — pre-customer scaffolding is the most expensive failure mode here.)

§B SCOPE / CEO LENS — Is this ambitious enough to matter, or so ambitious it never ships?
   What would the 10-star version be? Which premises should be challenged? Recommend ONE
   of: expand scope / hold scope / cut scope, with the reason.

§C ENGINEERING LENS — Architecture, data flow, state machine, failure modes, edge cases,
   test strategy. Back-of-envelope sizing FIRST (peak req/s, data/day) before choosing an
   architecture. Scale only the proven bottleneck, named with a number. Apply the standing
   defaults: serverless-first, agent-native (every surface reachable by human AND agent),
   programmable policy (no hardcoded limits/accounts/models).

§D DESIGN LENS — Only if there is a user-visible surface. Rate each design dimension 0-10
   and say what would make it a 10. If the deliverable is backend-only or the UX is
   conversational (SMS/voice), SKIP this and say so explicitly in the status line rather
   than generating filler; the design lens then runs later, gated on the first visual
   surface.

§E SUCCESS CRITERIA — Machine-checkable acceptance criteria. Binary, verifiable, each
   naming its verifier. If the feature has a probabilistic surface (LLM / agent /
   generation / recommendation), mark that these are the deterministic floor only and flag
   that /ai-done owns the eval bands, failure triage, tripwires, and rollback.

Ground every claim in the actual repo (read the code, do not assume). Flag anything you
could not verify as UNVERIFIED rather than asserting it.
```

## Gate before implementation

1. **Batch the genuine WHAT decisions into ONE question to the owner** (the house rule): user-facing behavior only, as yes/no or
   one-of-N. HOW decisions are yours to research and recommend, never to ask.
2. **the house rule plan review**: 1 fresh-context Claude reviewer + 1 Codex adversarial, in parallel. Text artifacts cap at
   **2 rounds**, apply must-fixes and re-run both once, then stop. Do not chase diminishing returns on text.
3. Write the plan to `<project>/docs/plans/` per the house rule, with the 3-tier naming convention.
4. Only then start coding, on a branch (the house rule).

## After implementation
Verify against the §E criteria on the real end-user surface (house-rule Live-Surface rule), not just the test harness.
Open and look at any rendered artifact (house-rule Visual Render Gate). For AI features, run `/ai-done`.
