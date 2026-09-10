---
name: ad-pipeline-skeleton
description: |
  The end-to-end shape of advertising work in four acts: DECIDE, MAKE, DISTRIBUTE, LEARN, with the
  human gates placed where being wrong is still cheap. Frames the brief, concept range, rising
  fidelity, automated QA, channel and placement choices, resourcing and pacing, the data plan,
  experiment design, and the feedback loop. Use when planning a pipeline, deciding stage order,
  working out where approvals belong, or auditing a pipeline that keeps producing expensive rework.
---

# The advertising pipeline: a skeleton

A reusable ordering system: plug the work in front of you into each stage, and keep the order and
the gates. It is worded for four operating modes, so read it against yours: a funded campaign, an
owned cadence, an earned or partner push you cannot schedule, an always-on programme.

## The whole arc in ten seconds

The order prevents two failures: producing instead of deciding, because production is cheap enough
to feel like thinking, and approving at the end, after the cost is already sunk.

| Act | What it settles | Stages |
|---|---|---|
| **DECIDE** | what you are saying, to whom, and why they would act | brief, concepts |
| **MAKE** | the thing itself, produced at rising fidelity | production, QA |
| **DISTRIBUTE** | where it appears, resourced how, run by whom | distribution plan, launch and operate |
| **LEARN** | what happened, whether it caused anything, what changes next | data plan, test, measure and feed back |

Four gates sit between them, three before anything irreversible and one after launch. One ordering
exception matters more than it looks: **the last act begins before the third finishes.**
Instrumentation added afterwards cannot recover the period it missed, so the data plan is written,
built and verified before launch.

---

# Act one: DECIDE

## Stage 1: the brief

One written artifact every later stage reads. Complete it before anything is produced.

```
PROPOSITION:     what is on the table, and what it does for the person considering it
                 (an offer is one case of this; so is a service, an invitation, an idea)
ACTION:          the single thing you want someone to do
DECISION TRUTH:  the real reason a person says yes, in plain language
SINGLE MESSAGE:  the one line they should remember if they forget everything else
AUDIENCE:        by motivation, not demographics. "people who tried something like this
                 and gave up", not an age bracket
ALLOWED:         claims you can support, with what evidence
FORBIDDEN:       claims you may not make, and who says so
SUCCESS OUTCOME: what a win looks like in the world, not on a dashboard
PRIMARY METRIC:  the one number that stands in for that outcome
GUARDRAIL:       a second number that must NOT get worse
```

The action is not always a purchase: applying, attending, enrolling, donating, switching, joining
or believing something new all qualify, and the decision truth follows from whichever you write.
Where a group decides, name the group and whose objection stops it. If the single message will not
fit on one line, the brief is not finished.

## Stage 2: concepts, plural

Produce a range, not a favourite. Range comes from spinning independent axes (the objection you
answer, the form, the tone, the length, the moment of contact), not from re-rolling one idea.

How wide is bounded at both ends, and neither end is a number you pick. The floor is structural:
every axis you spin appears at least once, or you have one idea wearing costumes. The ceiling is
the review: options stop earning their place once the reviewer ranks them against each other
instead of judging each against the brief. Cut an axis rather than skim.

Then cut against the brief. A concept dies here if it says nothing true, tries to say three things
at once, or nobody would pass it on.

---

# Act two: MAKE

## Stage 3: produce at rising fidelity

The principle: **settle each decision at the cheapest fidelity that can actually settle it, then
commit to the expensive form.** A message settles in a written line, a structure in an outline, a
layout in a rough arrangement, a claim in a sentence and its evidence. Committing to the finished
form first pays for the finish twice, plus a second approval.

The exception is a decision that only exists in the finished form (timing, pacing, a cut, how a
line lands when spoken): build a rough version of the real thing and say plainly that you are
approving a sequence, not a fragment.

Produce from what was approved rather than re-specifying it. Re-describing an approved decision
invites it to be reinvented, and the approved version quietly leaves.

## Stage 4: QA before a human looks

An automated pass against written criteria, so a person is never the first to notice an obvious
defect. Three properties matter more than the checklist:

- **Write the criteria before you see the output.** Criteria written afterwards describe what you
  got rather than what you wanted.
- **Withhold the production instructions from the judge.** The load-bearing part. A judge shown the
  instructions grades whether the output matched the request; you need to know whether it is any
  good. An independent judge helps with self-preference but does not fix a shared blind spot, and
  is the weaker of the two moves.
- **Cap the retry loop, and derive the cap.** Retrying an unchanged method re-samples the same
  distribution, so the question is when repeated failure beats bad luck as an explanation. Measure
  the per-attempt success rate and compute how often that run of misses happens by chance: at one
  in two, three misses turn up about one time in eight, which settles nothing; at four in five,
  about one time in a hundred and twenty five, which is your answer.

---

# Act three: DISTRIBUTE

## Stage 5: the distribution plan

Distribution is a stage, not a field in the brief. Producing without a placement map produces work
that fits nothing. The gate 3 form carries the full checklist (scheduling, targeting, exclusions,
rotation, frequency); these four are the parts people get wrong.

- **Channels come from where the decision is made** and how the audience already spends attention,
  not from what is available to you.
- **Paid, owned, earned behave differently.** Owned reach costs no media but is capped by the
  audience you have. Paid reach is rented and scales with budget. Earned reach cannot be scheduled,
  so plan it as a possibility with a trigger and a response, never as a forecast line.
- **Placements come before formats.** Each has a native form (aspect, length, character limits,
  sound on or off, skippable or not), so list placements first and derive the variants. Work
  resized into a placement it was not made for loses to work made for it.
- **Whatever is scarce gets paced.** Money, production slots, the few sends your audience
  tolerates, a partner's willingness to post: allocate per channel, release at a chosen rate, hold
  a reserve back to move behind whatever wins. Front-loading buys a faster read, even pacing a
  cleaner one.
- **Stop, hold and expand rules are written before launch.** Each is a number, a window and an
  action. A rule decided while watching a live number is not a rule.

## Stage 6: launch and operate

Live work is an ongoing operation, not a delivery. Name who watches it and how often, denser in the
first hours, and set a stand-down trigger that halts the activity without a meeting.

Do not make unplanned overlapping changes: changes nobody declared leave a result nobody can read.
Changing several at once is fine when it was designed that way, with the combinations and the
analysis written down before they went live. Log every change with its time either way.

---

# Act four: LEARN

## Stage 7: the data plan, written before launch

Measurement advice is not a data plan. A data plan is a structure, agreed and verified before any
spend and before any publication:

- **What is collected**, at what granularity, and which actions count.
- **Event naming.** A schema fixed before launch and versioned when it changes. A name reused for a
  new meaning silently corrupts every comparison across the boundary.
- **Where it lands**, and who owns that store.
- **Consent and privacy.** The basis for collection, what a refusal costs you in measurement,
  retention and deletion, and a hard rule against collecting a field you have no decision for.
- **Tracking QA before anything goes live.** Run one real action through the full path and confirm
  it arrives with the right attributes. A defect found afterwards cannot be repaired backwards.
- **Attribution window and rule**, fixed before results exist so it cannot be picked afterwards to
  flatter one.
- **Reporting.** Who reads what, how often, in what form.
- **Feedback.** How a finding becomes a line in the next brief. A pipeline with no path back is a
  factory, not a loop.

## Stage 8: test, because you cannot pre-decide what persuades

Everything before this is judgement, and judgement predicts persuasion badly. How you test depends
on what traffic you can get.

| Test type | Needs | Gives you | Does not give you |
|---|---|---|---|
| Paid test | committed budget, deliverable volume | comparison at real scale | a clean answer if the delivery system optimises between cells |
| Owned-audience test | an audience you already reach | fast, cheap comparison | anything about people you do not already reach |
| Qualitative or proxy test | recruited participants or a stand-in signal | why something fails, and early kills | any reliable estimate of size |
| Platform-native experiment | a channel offering controlled splits | assignment handled for you | control over how assignment actually works |

No test at all yields no evidence: name the risk you are accepting and move on.

**Duration.** Cover the audience's own rhythm (the cycle over which they are and are not reachable
and free to act) plus the lag between seeing and acting. Estimate both rather than inheriting a
fixed period, then fix the sample size per cell, the smallest effect worth detecting and the
stopping rule before the test starts.

**Reading it early.** Repeatedly checking a test designed for one look at the end inflates false
positives, which is why the old advice was never to look. Choose the analysis method up front
instead: a sequential or always-valid method has a stopping rule that already accounts for every
interim look, and under one of those the interim reads are legitimate. Deciding to peek after the
test has started never is.

**The counterfactual.** A race between your own options tells you which won, never whether the work
did anything. Pick a form your channels support: a held-out audience, a geographic split or a
matched-market comparison are the defaults. Unseen-ad controls such as placebo or ghost placements
are stronger, but they are advanced, depend on a channel offering them and sit close to platform
policy, so treat them as a specialist option. Some channels cannot hold out cleanly at all, because
you cannot withhold from someone who comes looking, or because coverage is broad and untargetable:
say so and name the substitute. "Compared with nothing" is also wrong whenever other activity runs
at the same time.

## Stage 9: measure and feed back

Read the primary metric AND the guardrail: single-metric optimisation reliably produces winners on
the metric that achieve nothing. Prefer a measure of lift over attribution where you can afford it.
Then write what won, what lost and why into the next brief. A finding that does not reach the next
brief was not learned, only observed.

---

## The gates, and what each one emits

Four gates, each producing an artifact rather than a nod. Everything between them can be automated.
The gates cannot: they exist where being wrong is unrecoverable.

| Gate, and where it sits | Emits | Because |
|---|---|---|
| Concept approval, before production spend | the chosen concepts, and why the others were cut | changing your mind is free here and expensive after |
| Rights and compliance, at two levels (below) | a clearance, and later a check of the finished item against it | a permission you do not hold leaves no trace in the output for an automated check to find |
| Launch, before the first irreversible step, whether committed spend or public release | the approved work, its placement and resourcing plan, a named person, a stand-down trigger, a rollback path | the irreversible step deserves a human |
| Post-launch review, before expanding, pausing, refreshing or redirecting | a decision, the numbers it rests on, the next review date | live work is a standing decision, and drift is the default |

The rights gate runs at two levels, and the levels are what pipelines get wrong. **Concept
clearance, before production spend**, asks whether this can be made and used at all: claims and
their evidence, likeness and voice, third-party material, personal data, regulated or sensitive
context. That is the expensive level, because a right you never held becomes a problem the moment
the work exists, not the moment it publishes. **Asset clearance, before anything is published or
used externally**, checks the finished item against that ruling and its disclosures. Internal
sketches you are neither spending on nor showing outside need neither level; gating those is
ceremony, and ceremony teaches people to route around gates.

## One pass through, worked

A public library wants more people using its digital lending service.

- **DECIDE.** Action: borrow one title this month. Decision truth: people assume a library card is
  for a building and for paper. Single message: your card already works from your sofa. Audience:
  cardholders who stopped, defined by stopping rather than by age. Guardrail: waits on popular
  titles must not lengthen.
- **MAKE.** Line and layout settle in a written line and a rough arrangement. A demonstration of
  the borrowing flow does not, so a rough version of the real thing is built and judged whole.
- **DISTRIBUTE.** Owned first: catalogue page, branch screens, member mailing. Earned held as a
  possibility with a trigger, since local coverage cannot be scheduled. Stop and expand rules
  written before anything runs.
- **LEARN.** Events named and verified end to end before launch. Counterfactual: one branch
  catchment held out. At review, first borrows against waiting times, and the finding goes into the
  next brief.

No budget line, no product, no sale, which is the test of whether the skeleton is doing its job.

## Auditing a pipeline that keeps producing rework

Ask these in order. The answer is usually a gate sitting after the commitment it was meant to
protect.

1. **Where is it discovered?** Name the stage that raises it, not the stage that fixes it.
2. **What was already committed when it surfaced?** Rework is nearly always a decision settled at a
   fidelity that could not settle it, then found at the fidelity that could.
3. **Did the gate before that point emit an artifact?** No artifact means the gate did not happen,
   whatever the calendar says.
4. **Did that artifact answer the question that later changed?** A nod cannot be audited; a form
   can, line by line, including its "not decided yet" line.
5. **Was an approved decision re-specified rather than carried forward?** If a later stage
   re-described it in fresh words, that is where the approved version left.
6. **Was there a written standard before the output existed?** If not, this is not rework but the
   first specification, arriving late and priced as a mistake.
7. **Is it the same rework every time?** Once is a miss. A repeat means the gate is in the wrong
   place: move it earlier, or lower the fidelity it judges, rather than staffing it harder.

## Using it

`references/gate-artifacts.md` carries the four gate forms, an experiment plan and an asset-naming
scheme. A small project can collapse two adjacent stages into an afternoon; the order and the gate
placement are the parts that stop the expensive mistakes rather than the slow ones.
