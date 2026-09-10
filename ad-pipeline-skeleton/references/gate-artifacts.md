# Gate artifacts

The four gates in the skeleton each emit a record. These are the records. Copy them into your
campaign folder and fill them in; a gate that produced no artifact did not happen.

They are deliberately short. A gate form long enough to be annoying gets rubber-stamped, which
is worse than not having one. Any line may be answered "not applicable", but it must be answered.

## Gate 1: concept approval

Sits before any production spend.

```
CAMPAIGN:                        DATE:
APPROVED BY:                     (a named person)

CHOSEN
  concept:               <name>
  objection it answers:
  form and length:
  the action it asks for:
  why this one:

CUT, and why (one line each; this half is the useful half)
  - <concept>: <reason>

NOT DECIDED YET (anything downstream must not assume):
```

The cut list matters more than the chosen list. It is what stops the same rejected idea returning
in three weeks with a new name.

## Gate 2: rights and compliance clearance

Two levels. **Level A, before production spend**, asks whether what you intend to make can be made
and used at all. **Level B, before anything is published or used externally**, checks the finished
item against that ruling. Internal sketches you are neither spending on nor showing outside need
neither.

### Level A: clear to produce

```
CAMPAIGN:                        DATE:
CLEARED BY:                      (name a person, not a team)

CLAIMS
  claim made:            supporting evidence:            holds up?
  <claim>                <document / test / source>      yes / no
  Any claim without evidence is cut here, not softened.

LIKENESS AND VOICE
  real person depicted or voiced?      yes / no
  if yes: written permission on file?  yes / no      scope / expiry:
  synthetic persona?                   yes / no      confirmed not resembling a real person?

THIRD-PARTY MATERIAL
  music / footage / typeface / mark used:
  permission and its scope (channels, territories, duration):

PRIVACY AND DATA
  personal data collected or used?         what, and on what basis:
  consent captured where required?         yes / no      how it is recorded:
  retention period and deletion path:

AUDIENCE AND CONTEXT SENSITIVITY
  targeting touches a sensitive category?  yes / no      if yes, permitted where you run?
  audience may include minors?             yes / no      what changes if so:
  regulated subject matter?                yes / no      which rules, and who confirmed:
  geography-specific requirements:         (list the jurisdictions checked)

VERDICT:   clear to produce  /  clear with conditions  /  not clear
CONDITIONS:
```

Re-run level A if the concept changes materially. A clearance covers what it described.

### Level B: clear to publish

```
CAMPAIGN:                        DATE:            CLEARED BY:
LEVEL A CLEARANCE REFERENCE:

  finished item matches what was cleared?          yes / no      what changed:
  every claim in it is on the level A list?        yes / no
  no depiction, voice or material appears that level A did not cover?    yes / no
  each channel's own advertising policy checked?   yes / no      by whom:

DISCLOSURE
  synthetic media or sponsorship disclosure required?   per channel:
  exact label text, and where it sits on the item:
  checked against the current requirement on (date):

VERDICT:   clear to publish  /  clear with conditions  /  not clear
```

Check the disclosure requirement now rather than trusting the last clearance: it moves faster than
most teams re-read it.

## Gate 3: launch

The irreversible one. Committed spend, or the work becoming public, or both.

```
CAMPAIGN:                        DATE:
APPROVED BY:                     (a named person)

ASSET SET
  each item: id / version / checksum:
  placement it is built for, with aspect and length:
  passed automated QA:      yes / no      criteria reference:
  gate 2 level B reference:
  disclosure label present and correct on each:      yes / no

PLACEMENT AND AUDIENCE
  channels, and the class of each:        paid / owned / earned
  placements per channel:
  audience definition and exclusions:
  geographies:

RESOURCING AND PACING
  what is scarce here:              (budget / production slots / sends / partner goodwill)
  allocation per channel:           total committed:
  pacing:                           (even / front-loaded / other, and why)
  reserve held back:
  stand-down trigger:               (a number, a window, and the action)

RUNNING IT
  launch window:                    sequencing across channels:
  frequency cap:                    rotation rule:
  who monitors, and how often:      first hours vs steady state:

DATA READINESS
  tracking verified end to end with a real action:   yes / no      by whom:
  attribution window and rule, fixed before launch:

ROLLBACK
  how this is taken down, and who can do it:
  how long that takes:
```

If the rollback line cannot be filled in, it is not ready to launch.

## Gate 4: post-launch review

Live work is a standing decision. This gate reopens it on a schedule instead of on a hunch.

```
CAMPAIGN:                        REVIEW DATE:            DECIDED BY:

WHAT THE NUMBERS SAY
  primary metric vs expectation:
  guardrail metric:                        moving the wrong way?  yes / no
  resource used to date vs plan:
  fatigue check:   response per additional exposure for the same audience,
                   this review vs the last:
                   is the fall bigger than the swing between quiet reviews?   yes / no

DECISION:   expand  /  hold  /  reallocate  /  refresh the pool  /  pause  /  stop
  what changes, exactly:
  what stays untouched, so the change stays readable:
  what this rests on (name the numbers, not the feeling):

NEXT REVIEW:
```

Change one thing per review by default, because a single change is the one you can still read
afterwards. Change several only by writing down here which combinations run and how you will
separate them. What is never allowed is several changes nobody planned.

## Experiment plan

Not a gate, but the stage most often run without a written plan, which is how a test ends up
measuring noise. Short is fine. Incomplete is not.

```
HYPOTHESIS:              if <change>, then <metric> moves <direction>, because <reason>
VARIANTS UNDER TEST:
TEST TYPE:               paid / owned audience / qualitative or proxy / platform-native / none
  if none:               the risk being accepted, in one line

COUNTERFACTUAL:          hold-out / geo split / matched market / unseen-ad control / none
  (the first three are the defaults; an unseen-ad control such as a placebo or ghost
   placement is stronger but needs a channel that offers it, plus a policy check)
  if none:               what you give up (you learn which won, not whether it worked)
  what else is running that could explain a change:

ASSIGNMENT
  who is eligible, and who is excluded:
  unit of assignment:                  (person / household / geography / time block)
  how assignment is randomised, and by what:

MEASUREMENT
  primary metric:                      guardrail metric:
  events tracked, by their agreed names:
  attribution window:
  data-quality checks before reading anything:
    delivery matched the plan across cells:        yes / no
    no assignment imbalance:                       yes / no
    tracking present on every cell:                yes / no

SIZE AND DURATION
  smallest effect worth detecting:
  sample size per cell, and how it was derived:
  duration, and the audience rhythm plus action lag it has to cover:

STOPPING RULE, written before the test starts:
  ship if:                kill if:                inconclusive means:
ANALYSIS METHOD:         fixed-horizon, read once at the end
                         /  a sequential or always-valid method whose stopping rule
                            accounts for every interim look
INTERIM READS:           none, unless the method above permits them
  if permitted:          at what points, and who acts on them
ANALYSED BY:             (a named person, ideally not the one who made the work)
```

## Asset naming

Measurement silently depends on this: you cannot compare things you cannot tell apart, and channel
reports give you back only the name you supplied.

Two rules, in this order:

1. **Key the analysis on the most stable identifier available.** Where the channel issues an id
   that does not change, record it and key on that, because names get edited, reused and truncated.
   Not every placement has one: some channels expose none, some exports drop it, and organic or
   earned appearances often have nothing. There, mint your own id and record whatever the channel
   does give back, so the two can be matched later.
2. **The name supplements the id, and carries nothing that is not already public.** A name typed
   into a third-party system is readable by everyone with access to that account and persists in
   exports long after the work is over. Treat every name as already outside: if what it encodes is
   not public yet, use a neutral slug and keep the mapping with your private records.

Agree one scheme before the first asset exists, keep every field even when empty, and never reuse a
name for a changed asset:

```
<campaign-slug>_<concept>_<form>_<aspect>_<length>_<variant>_<version>
```

The fields you will regret omitting are aspect, variant and version, because those are exactly the
dimensions you will later want to compare.
