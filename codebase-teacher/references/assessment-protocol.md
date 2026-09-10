# Assessment and progression protocol

Assess understanding after the learner finishes the section and its follow-up questions.

## Build the test from the actual session

Maintain a section question ledger with:

- question asked;
- concept behind the question;
- learner assumption or confusion;
- source inspected;
- resolved, partially resolved, or open;
- candidate transfer question.

The assessment should cover the report plus the concepts revealed by this ledger.

## Competencies

Score each applicable competency separately:

1. **Purpose**: explain why the module exists and why it matters.
2. **Placement**: locate it in the whole system and name upstream and downstream relationships.
3. **Mechanism**: trace a real flow through the code and data stores.
4. **Contracts and state**: explain inputs, outputs, invariants, and lifecycle.
5. **Failure reasoning**: predict important edge-case behavior and recovery.
6. **Quality judgment**: identify what exists, what is missing, and what best practice recommends.
7. **Change impact**: locate where a realistic change belongs and predict blast radius.
8. **Evidence judgment**: distinguish code evidence, deployment evidence, runtime proof, inference, and unknowns.

Do not let a high average hide failure in a critical competency. Use the mastery gate agreed with the learner or project.

## Question mix

Use five to eight questions per section, selected from:

- explain in the learner's own words;
- reconstruct or annotate a diagram;
- trace a user action or event;
- predict what happens if a dependency or assumption fails;
- compare the current design with an alternative;
- identify the correct place to make a change;
- evaluate a security, privacy, reliability, or performance trade-off;
- identify what evidence would be needed to strengthen a claim.

Prefer open explanation and diagram completion over multiple choice. Use multiple choice only when distinguishing plausible boundaries or failure outcomes.

## Feedback and repair

- Correct and well-supported: ask one transfer question.
- Correct but unsupported: ask for evidence or the missing path.
- Partially correct: identify the missing relationship and provide one focused visual hint.
- Incorrect: return to the whole picture, simplify the current piece, and retry.
- Unsupported by available evidence: teach the learner to say what remains unknown.

After a miss, retest only the missed competency with a different scenario. Do not repeat the same question or the entire section.

## Progress and review

Use simple learner-visible states:

- not started;
- learning;
- mastered;
- due for review.

Page views, reading time, and AI-generated summaries do not change mastery. Advance only on demonstrated recall and transfer.

For spaced review, vary the surface:

- revisit a prior concept inside a later module;
- ask for a cross-module connection;
- present a changed failure scenario;
- ask where a new requirement would land;
- require reconstruction of a diagram from memory.

If source code or a governing report changes materially, mark affected mastery as due for review instead of silently treating old understanding as current.
