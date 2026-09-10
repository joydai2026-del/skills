# Learning report template

Use this template for each module. Omit only sections that genuinely do not apply.

## 1. Why learn this now?

- State why the module matters to the system and to the learner.
- State what would break, become unsafe, or become hard to change without it.
- Name the prerequisites and what this module unlocks next.

## 2. Whole picture location

Show the stable whole-system diagram with the current module highlighted. Include upstream callers, downstream dependencies, stores, queues, third parties, and runtime placement.

Answer in one screen:

- Where are we?
- What enters?
- What leaves?
- Who depends on it?
- What does it depend on?

## 3. What exactly is it?

- purpose and responsibility;
- explicit non-responsibilities;
- public interface and contracts;
- state and data ownership;
- important invariants;
- domain vocabulary.

## 4. Why this design?

- problem and constraints;
- design rationale supported by code or decisions;
- plausible alternatives and trade-offs;
- label inferred rationale as inference.

## 5. Where does it live?

Map product journey, domain, repository or package, service, deployment unit, infrastructure, and owner when known. List key files only after the conceptual map.

## 6. When does it run?

Explain triggers, ordering, synchronous versus asynchronous work, lifecycle, schedules, retries, deadlines, and state transitions.

Use a sequence or state diagram when timing matters.

## 7. How does it work?

Trace one real, load-bearing operation:

```text
input -> validation -> decision -> transformation -> side effects
      -> persistence or event -> response -> downstream consequence
```

Include actual symbols and focused code excerpts only where they explain the mechanism.

## 8. Data inside and across the boundary

For each important datum, show:

- source and purpose;
- schema or shape;
- transformations;
- persistence, caching, and retention;
- consumers and third parties;
- sensitivity and access control;
- deletion or expiry behavior when relevant.

## 9. Normal path and edge cases

Show the normal flow first, then the most important failure or boundary flows. Include a failure-path diagram when multiple components interact.

## 10. Quality and best-practice comparison

Use the quality table from `quality-and-edge-cases.md`. Explain current controls, gaps, consequences, and authoritative best practices.

## 11. Evidence and unknowns

List citations with evidence labels. Separate confirmed facts, reasonable inferences, runtime-unconfirmed behavior, and open questions.

## 12. Questions this section should enable

List five to eight capability questions across:

- explain the purpose;
- trace the flow;
- predict a failure;
- compare a design alternative;
- locate a change;
- judge a quality trade-off.

Do not administer the assessment until the learner finishes follow-up questions.

## Diagram set

Use the smallest set that explains the topic, normally two to four diagrams:

1. whole-system location map;
2. module boundary or inside-the-box map;
3. sequence or data-flow diagram;
4. state or failure diagram when applicable.

Keep the same component names, colors, and flow direction across reports. Avoid decorative diagrams that add no information.
