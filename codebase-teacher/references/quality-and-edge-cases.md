# Quality and edge-case review

Apply only relevant checks. Research current official documentation for the actual language, framework, database, cloud, and protocol before recommending a change.

## Required comparison

| Dimension | Already done | Missing or risky | Recommended best practice | Evidence |
|---|---|---|---|---|

Never collapse these categories:

- confirmed bug;
- exposed vulnerability;
- risky design or weak control;
- missing test or missing evidence;
- obsolete or unsupported dependency;
- optional optimization.

## Correctness and bug resistance

- input, output, precondition, postcondition, and invariant checks;
- null, empty, malformed, boundary, overflow, duplicate, and out-of-order inputs;
- type safety, schema validation, error propagation, and partial failure;
- state-machine transitions and impossible states;
- unit, integration, contract, end-to-end, regression, property, and load tests;
- whether tests assert real behavior instead of implementation details;
- TODO, FIXME, HACK, disabled tests, swallowed errors, and silent fallbacks.

## Security

- authentication and authorization at every trust boundary;
- least privilege and separation of duties;
- input validation, output encoding, injection, path traversal, and unsafe deserialization;
- secret storage, credential rotation, logging, and client exposure;
- session, token, cookie, CORS, CSRF, replay, and rate-limit behavior;
- dependency and supply-chain risk;
- encryption in transit and at rest where relevant;
- auditability and incident response hooks.

Use the latest official OWASP ASVS or technology-specific security guidance when applicable.

## Privacy

- personal and sensitive data inventory;
- purpose, necessity, minimization, consent, and lawful basis when known;
- collection, transformation, sharing, third parties, retention, deletion, and export;
- access control, de-identification, re-identification risk, and logging exposure;
- whether cybersecurity controls alone leave privacy harms unresolved.

Use the current NIST Privacy Framework or applicable official regulatory guidance for general comparison. Do not give a legal conclusion from code alone.

## Reliability and resilience

- timeouts, deadlines, cancellation, retry limits, backoff, and jitter;
- idempotency, deduplication, ordering, and at-least-once delivery effects;
- circuit breaking, bulkheads, load shedding, and graceful degradation;
- dependency outage, stale cache, queue backlog, database failover, and network partition;
- health checks, readiness, cold start, recovery, rollback, and disaster recovery;
- SLI, SLO, error budget, capacity, and overload behavior;
- whether retries amplify a cascading failure.

Use current official SRE and provider guidance. Explain the interaction between reliability controls rather than praising each control in isolation.

## Performance and scalability

- latency distribution, throughput, concurrency, fan-out, and critical path;
- CPU, memory, network, disk, serialization, and connection-pool pressure;
- cache correctness and hit rate, N+1 access, unbounded queries, and pagination;
- algorithmic complexity and hot loops;
- frontend loading, responsiveness, layout stability, and rendering;
- scaling unit, bottleneck, cost curve, quota, and vendor limit;
- measurement evidence before optimization.

Use stack-specific official performance documentation. For web surfaces, use current Core Web Vitals and browser tooling.

## Observability and operability

- structured logs without secrets or PII;
- metrics tied to user-visible outcomes;
- distributed traces and correlation IDs;
- alert usefulness, ownership, and runbooks;
- deployment, feature flags, migrations, canaries, rollback, and configuration drift;
- whether a responder can distinguish symptom from root cause.

## Maintainability and change safety

- module boundaries, dependency direction, cycles, cohesion, and coupling;
- duplication, obsolete paths, feature flags, and dead code;
- public contracts, versioning, migrations, and backward compatibility;
- testability through real interfaces;
- ownership and blast radius;
- what must change to implement a realistic future request.

## Edge-case families

Cover the families that can materially change behavior:

1. missing, malformed, duplicate, stale, oversized, or unauthorized input;
2. first use, empty state, maximum scale, and boundary values;
3. concurrent requests, races, lock contention, and reordered events;
4. timeout, retry, cancellation, dependency failure, and partial success;
5. cache miss, stale cache, data corruption, schema mismatch, and migration in progress;
6. overload, quota exhaustion, degraded mode, and cascading failure;
7. configuration, environment, deployment, and version drift;
8. privacy request, data deletion, account compromise, and permission change;
9. restart, rollback, recovery, replay, and disaster scenarios.

For each selected edge case, answer:

- What triggers it?
- Where is it detected?
- What state may already have changed?
- What does the user see?
- Is retry safe?
- How is it observed and recovered?
- What test proves the intended behavior?
