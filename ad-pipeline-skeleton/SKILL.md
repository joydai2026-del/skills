---
name: ad-pipeline-skeleton
description: |
  How to run an advertising campaign with AI tools, end to end: write the brief, come up with
  concepts, make the copy and the static ads and the video ads, QA them, run them across channels,
  measure what worked, feed it into the next round. Four human gates, placed where being wrong is
  still cheap. Use when planning an ad campaign, setting up an AI ad pipeline, deciding what order
  to make things in, working out where approvals belong, or working out why a pipeline keeps
  redoing the same work. Includes a fillable brief and the form each gate has to produce.
---

# The AI ad pipeline: a skeleton

This is the order to make ads in, and where a human has to sign off.

Two things go wrong in almost every AI ad pipeline. People start generating images before they know
what the ad is supposed to say, because generating is cheap enough to feel like progress. And the
approval sits at the end, after the renders are paid for, which is exactly when changing your mind
costs the most.

The order below fixes both. It works for a product, a service, an event, a job opening, or a
public-health message with nothing to sell. Bring your own tools for each stage; the order and
the gates are the part worth keeping.

## The whole thing in ten seconds

| Act | What you settle | Stages |
|---|---|---|
| **DECIDE** | what the ad says, who it is for, why they would act | brief, concepts |
| **MAKE** | the actual ads: copy, stills, video | write, generate, QA |
| **DISTRIBUTE** | where they run, on what budget, cut for which placement | plan, launch, operate |
| **LEARN** | what happened, whether the ads caused it, what changes next | data plan, test, measure |

Four gates: concepts, rights and claims, launch, and a post-launch review. The first three sit
before something irreversible. One ordering note matters more than it looks: **the last act starts
before the third one finishes**, because tracking added after the spend cannot recover the days it
missed.

---

# Act one: DECIDE

## Stage 1: write the brief

One page that everything downstream reads. Fill it in before generating anything.

```
WHAT WE ARE SELLING:  the product, service, event or idea, in one sentence
THE ACTION:           what the viewer should DO (buy, sign up, book, apply, switch, show up)
WHO:                  by motivation, not age brackets. "people who tried this and gave up",
                      not "women 25-40"
WHY THEY SAY YES:     the honest reason someone actually takes that action
THE ONE LINE:         what they should remember if they forget everything else
CAN SAY:              claims you can back up, and with what
CANNOT SAY:           claims you may not make, and who says so
WHERE IT RUNS:        the channels, and the formats each one needs
WINS IF:              the one number that decides success
DOES NOT BREAK:       a second number that must not get worse
```

If THE ONE LINE does not fit on one line, the brief is not finished, and no amount of production
polish downstream will rescue the ad.

## Stage 2: concepts, plural

Write several genuinely different ads, not one idea with three headlines. Difference comes from
changing what the ad *does*: which objection it answers, whether it demonstrates or jokes or
testifies, whether it runs six seconds or sixty, whether it is a still or a film.

Cover each of those at least once, and stop where a reviewer can still judge them all properly in
one sitting.

Then cut. A concept dies here if it says nothing true, tries to say three things at once, or nobody
would send it to a friend.

> **Gate 1: concept approval.** Before any production spend. Produces the chosen concepts and, more
> usefully, the cut list with one line each on why.

> **Gate 2: rights and claims.** Two levels. Clear the CONCEPT before you generate anything, because
> a face, a voice, a song or a claim you do not have the rights to is a problem the moment it exists,
> and waiting until you post means paying to create the liability first. Then clear the FINISHED
> FILES before they go out, since what got made is never exactly what was pitched. Internal sketches
> need neither. A likeness detector catches some of this; it never catches a licence nobody granted.

---

# Act two: MAKE

## Stage 3: copy first, then stills, then video

Settle each decision in the cheapest medium that can actually settle it.

- **Copy settles the message.** A headline and a body line tell you whether the ad has a point. Do
  this before any image exists.
- **Stills settle the look.** Composition, palette, how the product is shown, typography. A still
  costs a fraction of a video and every one of those decisions is already visible in it.
- **Video settles the timing.** Only once the look is signed off.

Fixing the look after you have paid to animate it costs the render again plus a second approval
round. That is the entire reason for this order.

**The exception that matters:** some ads only exist in motion. If the idea is a reveal, a punchline,
a cut, or a beat of timing, judging it as a still selects for ads that photograph well rather than
ads that work. Storyboard those, and accept that you are approving a sequence.

**Generate the video from the approved still**, and describe the MOVEMENT rather than re-describing
the picture. Re-describe the picture and the model reinvents it, taking the approved look with it.

## Stage 4: QA before a human looks

Run an automated pass against written criteria, so a person is never the first to spot a broken
hand, garbled text, or a product that came out the wrong shape. Three things matter more than the
checklist itself:

- **Write the criteria before you see the output.** Criteria written afterwards just describe what
  you got.
- **Do not show the judge the prompt.** This is the load-bearing part. A judge holding the prompt
  grades whether the picture matches the request; what you need to know is whether the ad is any
  good. Using a different model helps a little. Withholding the prompt helps a lot.
- **Cap the retries, and work out the cap.** Re-running an unchanged prompt re-rolls the same odds.
  How many misses prove the prompt cannot express the thing depends on how often it lands: if a
  requirement succeeds about half the time, three misses settle nothing; if it succeeds four times
  in five, two misses are already your answer. Then change the method, not the seed.

---

# Act three: DISTRIBUTE

## Stage 5: the distribution plan

Decide these before launch day, not on it:

- **Channels**, and what each actually needs: aspect ratios, lengths, safe areas, whether sound is
  on by default, whether it can be skipped.
- **Cutdowns per placement.** One hero film becomes a square, a vertical, a six-second and a
  thumbnail. Work resized into a placement it was not cut for usually loses to work made for it.
- **Paid, owned and earned**, and which of the three is doing the actual lifting.
- **Schedule and pacing**, including how fast you are willing to spend while you still know nothing.
- **Rotation**, so the same person is not shown the same ad until they resent it.

## Stage 6: launch and operate

A live campaign is something you run, not something you finish. Someone owns it daily: watching
delivery, watching the guardrail number, pulling anything that misbehaves. Decide the stop-loss and
who is allowed to pull the campaign BEFORE it is live, because that is the worst decision to make
at speed.

> **Gate 3: launch.** The irreversible one. A named person, a date, the approved files, and a
> rollback someone has actually confirmed they can carry out.

---

# Act four: LEARN

## Stage 7: the data plan, written before launch

Decide these while you can still change them:

- **What counts as the action**, defined once, the same way everywhere.
- **How each ad is named**, so the report can tell them apart. This is the boring one that silently
  breaks everything else: you cannot measure ads you cannot tell apart.
- **Where the numbers land**, who reads them, and how often.
- **Consent and privacy**, checked against the rules where you are running.
- **Confirm the tracking fires before you spend.** A campaign with broken tracking is a campaign you
  will argue about for a month.

## Stage 8: test, because you cannot decide in advance what persuades

Everything up to here was judgement, and judgement predicts persuasion badly.

Run the surviving ads against each other on a deliberately small committed budget. It costs money;
the point is that it costs far less than being wrong at full spend. An owned audience, an email list
or a small organic push can stand in where paid testing is not available, and running no test at all
is a choice worth making on purpose rather than by default.

Decide the stopping rule BEFORE it starts: how many people per variant, how long, and what result
would make you kill each one. Read it early and you are reading noise, unless your analysis method
was built for interim looks.

**Name the counterfactual.** A race between your own ads tells you which ad won. Only a holdout, a
geo split or a matched market tells you whether the campaign did anything at all.

## Stage 9: measure, then feed it back

Measure the winning number AND the guardrail. Optimising one number reliably produces ads that win
on it and sell nothing: the ad with the best click rate can be the worst ad you ran.

Prefer a measure of lift over last-click attribution where you can afford one. Last-click tells you
which ad was nearby when someone acted, not whether it caused them to act.

Then write down what won and why, and put it in the next brief. A pipeline with no feedback step is
a factory, not a loop.

> **Gate 4: post-launch review.** Scale it, pause it, refresh the creative, or move the budget. A
> live campaign is a decision that stays open.

---

---

## One ad, all the way through

A community bike shop wants more people booking winter servicing. Invented, deliberately dull, and
it shows what each stage actually hands to the next.

**Brief.** Selling: a winter service, booked online. Action: book a slot. Who: people whose bike has
been in the shed since October and who suspect it needs something. Why they say yes: they do not
want to find out it is broken on the first good day. The one line: *"Find out now, not in March."*
Cannot say: anything about safety outcomes we cannot support. Wins if: bookings. Does not break:
cost per booking.

**Concepts.** Five, deliberately unalike: the shed shot (guilt), the first ride of spring (reward),
a mechanic naming the three things that always seize, a six-second before-and-after, a joke about
the bike you swore you would ride all winter. The mechanic one and the six-second one survive; the
joke gets cut for saying nothing true. *That cut line is the useful half of the record.*

**Make.** Copy first: the headline is the one line, the body is one sentence about the slot. Stills
next: the bike against the shed wall, cold light, no people. Only once that is signed off does the
six-second cut get animated from the approved still, with the movement described (*the door opens,
light falls across the frame*) rather than the picture described again.

**QA.** A judge that never sees the prompt checks the ads for garbled text and a bike with the wrong
number of gears, before anyone on the team looks.

**Distribute.** Square and vertical cuts, sound off by default so the line is on screen, running
where local people actually are. Small budget, a rotation cap so the same person does not see the
shed four times.

**Learn.** Bookings tagged so each cut is distinguishable in the report, a holdout postcode that
sees nothing, and a note in the next brief: the mechanic naming parts outperformed the mood piece,
so the next round makes two more like it.

## When the same rework keeps happening

Ask these in order. The answer is usually a gate sitting after the commitment it was meant to
protect.

1. **Where is it caught?** Name the stage that raises it, not the stage that fixes it.
2. **What had already been paid for when it surfaced?** Rework is nearly always a look or a message that
   got waved through on a headline and only fell apart once someone paid to animate it.
3. **Did the gate before it produce a filled sheet?** An empty one means the gate did not happen,
   whatever the calendar says.
4. **Did that sheet answer the question that later changed?** A nod cannot be audited. A filled
   sheet can, including its "not decided yet" line.
5. **Did someone re-describe an approved ad instead of using it?** If a later stage wrote the brief
   again in its own words, that is where the approved version quietly left.
6. **Was there a written standard before the work existed?** If not, this is not rework. It is the
   first specification, arriving late and priced as a mistake.
7. **Is it the same rework every time?** Once is a miss. A repeat means the gate is in the wrong
   place: move it earlier, or have it judge a cheaper draft, rather than staffing it harder.

## Two rules that outlast every tool

- **Never fake evidence.** No invented testimonial, review, endorsement, or screenshot of something
  that did not happen. Past the legal exposure, it destroys the trust the ad was buying.
- **Disclose AI-generated media** where the platform or the law requires it, and check that
  requirement at the moment you publish rather than trusting what was true last quarter. These rules
  move faster than most teams re-read them.

## Using it

`references/gate-artifacts.md` carries the four gate forms, a test plan and a naming scheme. They
are short on purpose: a form long enough to be annoying gets rubber-stamped, which is worse than not
having one.

Take the order and the gates; bring your own tools and your own craft for each stage. A small
campaign can do stages 2 and 3 in an afternoon. What is worth keeping is that nothing expensive
happens before the cheap decision that governs it.
