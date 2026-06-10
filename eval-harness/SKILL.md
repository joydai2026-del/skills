---
name: eval-harness
description: |
  Evaluation-driven development (EDD) for agent projects. Define capability evals,
  run regression suites, grade results (code-based, model-based, human), track metrics
  over time. Use when: "eval harness", "evaluation framework", "test harness", "create evals",
  "benchmark accuracy", "run evals", "test agent quality", "regression check", "eval suite",
  "grade outputs", "eval report", "confidence interval on a pass rate", "how many test cases
  do I need", "is this improvement real", "is 90% good", "quantify the eval", "what pass rate
  is good enough".
  PROACTIVE TRIGGER: When building AI features that need quality measurement. Suggest when
  user is building agent features or LLM-powered capabilities that need measurable quality gates.
allowed-tools:
  - Bash
  - Read
  - Write
  - Edit
  - Grep
  - Glob
last_used: "2026-06-05"
gotchas_count: 0
---

# Eval Harness — Evaluation-Driven Development

Evals are to agent development what tests are to traditional software. This skill
provides a structured workflow for defining, running, grading, and tracking evaluations
against agent features, LLM outputs, or any system where correctness is probabilistic
rather than binary.

## When to Activate

- Building or modifying agent capabilities
- Validating LLM prompt changes
- Checking for regressions after code changes
- Establishing quality baselines for new features
- Comparing model or prompt variants
- Any time "does this work?" requires more than a unit test

## Core Concepts

### 1. Eval Types

**Capability Evals** — Does the feature do what it should?
- Define input/output pairs that cover the feature's requirements
- Run the feature against all test cases
- Grade results against expected outputs
- Use when building new features or validating prompt changes

**Regression Evals** — Did we break anything?
- Maintain a baseline of passing evals (the "golden set")
- After changes, re-run the full suite
- Any previously-passing case that now fails is a regression
- Use after every significant change before shipping

### 2. Grader Types

**Code-based graders** (deterministic, fast):
- `exact_match` — output must equal expected exactly
- `contains` — output must contain a substring
- `regex` — output must match a pattern
- `json_schema` — output must validate against a JSON schema
- `numeric_range` — numeric output within min/max bounds
- `custom` — run a shell command, exit 0 = pass

**Model-based graders** (nuanced, slower):
- Use Claude to judge output quality against a rubric
- Best for open-ended outputs where exact matching is too rigid
- Always provide a rubric (what makes a good answer vs. a bad one)
- Returns a score (1-5) and reasoning

**Human graders** (highest fidelity, slowest):
- Flag cases for manual review
- Record the human judgment for future regression checking
- Use sparingly for cases where automated grading is unreliable

### 3. Metrics

| Metric | Definition | Use When |
|--------|-----------|----------|
| `pass@1` | Passes on the first attempt | Default for deterministic features |
| `pass@k` | Passes within k attempts | For stochastic features (LLM outputs) |
| `accuracy` | % of test cases passing | Overall suite health |
| `regression_count` | Cases that went from pass to fail | After changes |
| `mean_score` | Average model-graded score (1-5) | Quality tracking over time |
| `pass_rate ± CI` | Accuracy with a Wilson 95% confidence interval | Any reported rate (see 4) |
| `mcnemar_p` | Significance of a before/after change on a fixed set | Comparing two variants (see 4) |

> Every rate above is meaningless without a sample size N and a confidence interval, and must never be one blended number. Core Concept 4 is how you turn a raw percent into a number you can defend.

### 4. Quantifying the result (how good is "good"?)

There is no universal "good" pass rate. Set the bar by the cost of being wrong, then make the number trustworthy with three rules.

#### Rule 1: Two buckets, never one blended number

Split every criterion into a hard gate or a quality criterion. They have opposite bars and are reported on separate lines.

| Bucket | Examples | The bar |
|--------|----------|---------|
| **HARD GATE** | safety / harmful output, valid output format, auth, billing, correctness-critical facts | **100% / zero violations.** One failure fails the whole run. Never averaged into a score. Test against adversarial / worst-case inputs, not the average case. |
| **QUALITY** | tone, completeness, helpfulness, style, formatting | severity-weighted **0-100 score, track the trend.** ~90-95% is a common user-facing convention, not a law. |

Most binary success criteria are hard gates, so their honest target is 100%. The "what percent is good?" question only really applies to the quality bucket. Your report reads as two lines, never merged into "92% pass":

```
Gates:   12/12 PASS  (safety 0 violations / 200 adversarial inputs)
Quality: 84/100      (worst slice: factual-accuracy 61% on long inputs, N=140, 95% CI 53-69%)
```

#### Rule 2: No percent without an N and a Wilson confidence interval

A pass rate from 20 cases is a guess wearing a lab coat. Report the point estimate plus a Wilson interval (not the naive normal approximation, which lies at the extremes).

| Sample size N | What "90% pass" actually means (Wilson 95% CI) | Verdict |
|---|---|---|
| N = 20 | true rate is somewhere in **70% - 97%** | useless for decisions |
| N = 100 | true rate is roughly **83% - 95%** | actionable floor (±10%) |
| N = 385 | true rate is roughly **87% - 93%** | tight (±5%) |

**Sample-size floor: ~100 cases for a ±10% margin** (the practical floor to quantify a rate); ~385 for ±5%; ±1% needs ~9,600. The "10+ cases" minimum elsewhere in this skill is for COVERAGE (a smoke suite that exercises the paths), which is not the same as enough data to quantify a rate.

```python
# Wilson score interval for x passes out of n (default 95%).
def wilson(x, n, z=1.96):
    import math
    if n == 0: return (0.0, 0.0)
    p = x / n
    d = 1 + z*z/n
    center = (p + z*z/(2*n)) / d
    half   = z*math.sqrt(p*(1-p)/n + z*z/(4*n*n)) / d
    return (round(center - half, 4), round(center + half, 4))

# Sample size needed for margin E (p=0.5 worst case): N ≈ z²·p(1-p)/E²
def n_for_margin(E, z=1.96, p=0.5):
    import math
    return math.ceil(z*z*p*(1-p) / (E*E))   # E=0.10 → 97, E=0.05 → 385
```

**100% is a red flag, not a trophy.** A perfect score usually means the eval is too easy, saturated, or leaked (8-18% of HumanEval overlaps common training data). By the rule of three, 0 failures in 20 cases still allows a true failure rate up to ~15%. If you hit 100%, add harder cases.

#### Rule 3: Gate per slice, not just the headline

A single blended number hides which part is broken. One real team reported 95% across 400 cases while a specific edge-case category failed 60% of the time, undiscovered until a customer complained. Report pass rate **per criterion AND per segment** (user type, input length, language), and always surface the WORST slice. Use one evaluator per dimension so you can see which dimension drags the score down. An 85% blend could be 100% on tone and 55% on factual accuracy.

## Procedure

### Step 1: Define the Eval Suite

Create an eval suite file at `evals/<suite-name>.yaml`:

```yaml
# evals/agent-routing.yaml
suite: agent-routing
description: "Tests that the router sends queries to the correct specialist agent"
version: 1
created: 2026-03-31

cases:
  - id: route-code-question
    input: "How do I fix this TypeScript error?"
    expected: "code-agent"
    grader: exact_match
    tags: [routing, code]

  - id: route-ambiguous
    input: "Help me with my project"
    grader:
      type: model_based
      rubric: |
        Score 5: Asks clarifying question before routing
        Score 4: Routes to general agent with explanation
        Score 3: Routes to a reasonable agent
        Score 2: Routes to wrong agent but recovers
        Score 1: Routes to wrong agent, no recovery
      threshold: 3
    tags: [routing, ambiguous]

defaults:
  timeout: 30
  retries: 1
```

### Step 2: Write the Runner

Connect the eval suite to the system being tested. Create `evals/run-<suite>.sh`:

```bash
#!/bin/bash
SUITE_FILE="evals/agent-routing.yaml"
RESULTS_FILE="evals/results/agent-routing-$(date +%Y%m%d-%H%M%S).jsonl"
mkdir -p evals/results

# For each case: extract input, run through system, capture output, grade, write JSONL
# Output format per line:
# {"case_id":"route-code","input":"...","output":"code-agent","expected":"code-agent","grade":"pass","grader":"exact_match","duration_ms":1200}
```

### Step 3: Run the Evals

```bash
bash evals/run-agent-routing.sh              # Full suite
bash evals/run-agent-routing.sh --tags code  # Filtered by tag
bash evals/run-agent-routing.sh --case route-ambiguous  # Single case
```

### Step 4: Generate the Report

```
## Eval Report: agent-routing
Date: 2026-03-31
Suite version: 1

### Summary (two buckets, never one blended number; see Core Concept 4)
- Gates:   8/8 PASS (0 violations across 8 hard-gate cases)
- Quality: weighted score 81/100 | raw accuracy 10/12 = 83.3%, Wilson 95% CI 55.2-95.3% (N=12, too small to trust)
- Worst slice: route-edge category 1/3 = 33% (N=3). The headline average hides this.
- Regressions: 1
- N note: 12 cases is a coverage smoke, not enough to quantify a rate. ~100 for a ±10% CI.

### Failed Cases
| Case ID | Grader | Expected | Got | Details |
|---------|--------|----------|-----|---------|
| route-edge | exact_match | "code-agent" | "general-agent" | Misrouted |

### Regressions (vs. baseline)
| Case ID | Previous | Current |
|---------|----------|---------|
| route-with-context | pass | fail |
```

### Step 5: Track Over Time

Append to `evals/eval-history.md`:

```markdown
| Date | Version | N | Passed | Accuracy | 95% CI (Wilson) | McNemar vs prev | Regressions | Notes |
|------|---------|---|--------|----------|-----------------|-----------------|-------------|-------|
| 2026-03-31 | 1 | 12 | 10 | 83.3% | 55.2-95.3% | baseline | 1 | Baseline (N too small to trust) |
| 2026-04-02 | 1 | 12 | 11 | 91.7% | 64.6-98.5% | not significant | 0 | "Fixed routing", but CI overlaps, 1 net flip |
```

The CI column is the honesty check: when two rows' intervals overlap heavily, the apparent improvement is probably noise. Widen N before claiming a real gain.

## Model-Based Grading

When using model-based grading, structure the prompt:

```
You are grading an AI agent's output. Score 1-5 based on this rubric:

RUBRIC: {rubric}
INPUT: {input}
OUTPUT: {actual output}
EXPECTED (reference): {expected}

Respond with ONLY: {"score": <1-5>, "reasoning": "<one sentence>"}
```

Rules:
- Always include a rubric. Never ask to "judge quality" without criteria.
- Set a threshold. Default: 3.
- Log reasoning for debugging.
- Expect some variance in model-based grades.

### Calibrate the judge before you trust its scores

An LLM-as-judge inherits every weakness of the model it judges. Before its scores are allowed to gate anything, check it against human labels on a sample of cases:

| Metric | Bar |
|--------|-----|
| **Cohen's kappa** (chance-corrected agreement) | kappa >= 0.6 acceptable; kappa ~ 0.8 is human-level |
| Recall on the failure class | higher is better; watch the false-positive rate too |

Use kappa, NOT raw "% agreement": a judge that always says "pass" looks 90% accurate on a 90%-pass set while catching zero real defects. Best single-model judges reach kappa ~ 0.78 (right at the human boundary); a 3-judge ensemble can reach ~0.95. Mitigate position bias (randomize answer order), verbosity bias (longer is not better), and self-preference (a model favoring same-family outputs).

## Regression Workflow

1. Run full eval suite (not just affected cases)
2. Compare against last passing run (baseline)
3. Flag regressions
4. Decision: 0 regressions = ship. Regressions = fix first.
5. Update baseline after shipping

### Is the before/after change real? McNemar's paired test

On a FIXED test set, "we went from 88% to 91%, we improved!" is usually noise. Comparing two raw percentages is the wrong move: only the cases that FLIPPED carry information. State the hypothesis first ("the new prompt beats the old by at least 3 points"), then on the same N cases count:

- **b** = passed before, failed after (new regressions)
- **c** = failed before, passed after (new fixes)

```
chi-square = (b - c)^2 / (b + c)     # 1 dof; > 3.84 means p < 0.05 (the change is significant)
```

If `b + c < 25`, the chi-square approximation is unreliable; use the exact binomial test instead (probability of seeing this split under a fair coin, p=0.5). Cases that did not change are irrelevant to the test. A 3-point move on 100 cases is only ~3 net flips, which is almost never significant. Caveat: cases drawn from the same source document are clustered and can inflate the true error bar ~3x, so a naively "significant" result can still be a false alarm.

```python
def mcnemar(b, c):
    # b = pass->fail, c = fail->pass. Returns the two-sided p-value (a scalar in [0,1]).
    import math
    from math import comb
    n = b + c
    if n == 0: return 1.0
    if n < 25:                        # small-sample: exact two-sided binomial (fair coin)
        k = min(b, c)
        return round(min(1.0, 2 * sum(comb(n, i) for i in range(0, k + 1)) / (2 ** n)), 4)
    chi = (abs(b - c) ** 2) / n        # McNemar chi-square, 1 dof
    return round(math.erfc(math.sqrt(chi / 2)), 4)   # p-value via chi-square(1) survival

# derive significance at the call site:
#   p = mcnemar(b, c); significant = p < 0.05
```

Ship rule with significance: 0 regressions AND (you are not claiming an improvement OR McNemar confirms the gain).

## File Structure

```
evals/
  <suite>.yaml            # Suite definition
  run-<suite>.sh          # Runner
  results/                # Raw JSONL results
  eval-history.md         # Trend tracking
  baselines/              # Last known-good results
```

## Quick Start

When invoked with `/eval`:

1. Ask: "What are we evaluating?"
2. Check if `evals/` exists. Create if not.
3. Check for existing suite. Offer to run or extend.
4. If new: define 10+ test cases for COVERAGE (happy path + edge cases + failures). To QUANTIFY a pass rate you trust, aim for ~100 (see Core Concept 4); 10 is a smoke, not a measurement.
5. Write suite YAML + runner
6. Run evals, generate the report as TWO buckets (hard gates at 100% + a weighted quality score), each rate with an N and a Wilson 95% CI, and the worst slice surfaced (Core Concept 4)
7. Append to eval-history.md (with the CI column)
8. Flag regressions clearly; for any before/after claim, run McNemar on the fixed set, do not compare two raw percentages

## Anti-Patterns

- **Too few cases**: 3 is not a suite. 10+ for coverage; ~100 before you quote a pass rate as if it means something.
- **A percent with no N and no CI**: "90% pass" on 20 cases means the true rate is anywhere from 70% to 97%. Always report N and a Wilson 95% CI.
- **One blended number**: averaging safety gates into a quality score hides the failure. Split into hard gates (100%) and a weighted quality score, two separate lines.
- **Comparing two raw percentages**: "88% to 91%, we improved!" on a fixed set is usually 3 net flips of noise. Use McNemar.
- **Trusting the aggregate**: a 95% headline can hide a slice failing 60%. Gate per slice and surface the worst one.
- **Treating 100% as a trophy**: it usually means the eval is too easy, saturated, or leaked. Add harder cases.
- **An uncalibrated LLM judge**: model-based scores that were never checked against human labels (kappa).
- **Only happy path**: No failure/edge cases = decorative eval.
- **Exact match on LLM output**: Use contains, regex, or model_based instead.
- **No baseline**: Can't detect regressions without one. Save the first passing run.
- **Stale suites**: Feature changed but eval didn't. Update when requirements change.
- **Grading without rubrics**: Specify what good and bad look like.

## Source

The quantification rules in Core Concept 4 + McNemar + judge calibration are distilled from `~/Documents/jj-knowledge-vault/wiki/ai-ml/eval-quantification-best-practices.html` (researched 2026-06-04 from Anthropic "Adding Error Bars to Evals" arXiv 2411.00640, OpenAI eval best-practices, UK AISI Inspect, Hamel Husain's Evals FAQ, Eugene Yan, and the Wilson / McNemar references). Read that page for the full sourcing and the risk-tier threshold cheat-sheet.
