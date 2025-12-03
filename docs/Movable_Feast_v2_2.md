Movable Feast v2.2 (Core Study)
A Minimal, Powered, Peer-Review-Ready Benchmark for Movable Holiday Reasoning in LLMs
0. Purpose of v2.2 Core

The original v2.1 was conceptually strong but operationally overloaded.
v2.2 is the reduced, powered, feasible backbone — the version you can execute now, with:

correct statistics

correct sample sizes

unambiguous ground truth

minimal confounds

clean hypotheses

reproducible methodology

v3.0 can expand later.
v2.2 is the foundation.

1. Core Research Question

Do LLMs generalize temporal reasoning for movable holidays across years, languages, and prompt scaffolds — without relying on memorized tuples or examples?

This study limits itself to three holidays, two languages, and four prompt conditions, enabling enough statistical power to make real claims.

2. Hypotheses (Corrected Null Forms)
H1 — Temporal Generalization (Equivalence)

Claim: Models that succeed in recent years maintain performance for past and future years.
Null (H1₀): Performance differs across year-strata by more than ±5 percentage points.
Test: 2×1 TOST equivalence test; α=0.05; 95% CI.

H2 — Movable vs Fixed Holiday Difficulty (Superiority)

Claim: Movable holidays are significantly harder than fixed-date controls.
Null (H2₀): No accuracy difference between movable and fixed tasks.
Test: Logistic mixed-effects model; holiday-type fixed effect; FDR-corrected.

H3 — Prompt-Condition Sensitivity (Superiority)

Claim: Prompt scaffolds impact accuracy.
Null (H3₀): No prompt-condition effect larger than ±10 percentage points.
Test: Pairwise contrasts via GLMM; BH correction.

H4 — Synthetic-Rule Non-Generalization (Superiority)

Claim: Models cannot generalize novel synthetic calendars from few-shot examples.
Null (H4₀): Accuracy on synthetic calendars is equivalent (±5%) to natural holidays.
Test: TOST equivalence test.

3. Scope of v2.2 Core
3.1 Languages

Mandarin Chinese (zh) — for lunisolar context

English (en) — universal control

No Vietnamese/Korean yet (added in v3.0).

3.2 Holidays
Movable Holidays

Chinese New Year (Lunisolar)

Eid al-Fitr (Islamic, Umm al-Qura)

Western Easter (Gregorian Computus)

Fixed Controls

Christmas (Dec 25)

3.3 Year Strata

Selected for diversity, memorization detection, and edge-case coverage:

Past: 1995–2005

Recent: 2015–2025

Future: 2030–2035

Stress Years: 2033–2034 (Chinese lunisolar anomaly)

Total years per holiday: 25 years

3.4 Prompt Conditions (Mechanism Ladder Core)

minimal — plain Gregorian date

CoT-minimal — reasoning outline, no examples

examples-only — prior-year mappings, no reasoning scaffold

resolver-correct — explicit injected mapping (oracle)

Removed for now: resolver-wrong, partial-resolver, CoT-full, synthetic rules (except for H4), perturbations.

4. Ground Truth Specification (Frozen)

To eliminate ambiguity, v2.2 fixes all conventions explicitly:

4.1 Chinese New Year

Source: PRC civil calendar

Time zone: Beijing time (UTC+8)

Compute using: lunardate (verified vs official tables)

4.2 Eid al-Fitr

Method: Civil Umm al-Qura calendar (not observational)

Time zone: Mecca (UTC+3)

Library: ummalqura verified with official MoFA tables

4.3 Western Easter

Method: Gregorian Computus (Meeus / Butcher)

Time zone: UTC

Implementation: Pure Python function

4.4 Christmas

Always December 25 (Gregorian), no ambiguity.

All gold labels are precomputed and frozen into a CSV/Parquet dataset.

5. Sample Size & Power (Corrected)

We want to detect 10-point differences in accuracy (e.g., 80% vs 90%).
Power analysis for two-proportion tests (α=0.05, power 0.8) gives:

N = 90 samples per condition per holiday (rounded to 100 for convenience)

Thus:

100 items × 4 prompt types × 4 holidays × 2 languages
= 3,200 queries per model

This is manageable and statistically valid.

Negative controls: 1:1 ratio → +3,200
Total per model: ~6,400 calls

At 8 models → 51k calls (not 300k like v2.1).
Feasible.

6. Dataset Construction
6.1 Positive Cases

For each holiday × year, generate corresponding Gregorian date.

For Chinese New Year, Eid, Easter: programmatically computed.

For Christmas: fixed.

6.2 Negative Cases

Per year, sample:

1–2 random non-holiday dates

±1-day near-dates

impossible dates (Feb 30, Apr 31)

Negatives labeled as “none”.

7. Prompts (Frozen)

Each language has aligned templates.

minimal (zh example)
请判断日期：{{gregorian_date}} 是否是节日？请输出 JSON:
{"holiday": "..."}

CoT-minimal
请逐步推理日期是否对应节日，但不要使用示例。输出 JSON:
{"holiday": "..."}

examples-only
示例：
{{prev_year}}-{{date}} = {{holiday}}
请判断：{{target_date}} 是否是节日？
输出 JSON:
{"holiday": "..."}

resolver-correct
以下是日期映射：
{{target_date}} -> {{correct_holiday}}
请输出 JSON:
{"holiday": "..."}


JSON schema: deterministic, strict.

8. Evaluation Pipeline

Send prompt → model (temp=0, top_p=1).

Retry once if JSON invalid.

Parse deterministic schema.

Score:

holiday correct

holiday incorrect

false positive

refusal

Log:

raw response

provider metadata

model version

timestamp

9. Statistical Analysis
Primary model:

Logistic mixed-effects regression (lme4 / statsmodels):

correct ~ prompt_condition + year_stratum + holiday + language
        + (1 | model) + (1 | item) 

Tests:

H1: TOST equivalence test ±5% window

H2: holiday-type fixed effect

H3: prompt-condition contrasts

H4: synthetic vs natural equivalence

Corrections:

Multiple comparison control: Benjamini–Hochberg

Confidence intervals: Clopper–Pearson + bootstrap

10. Reproducibility

We freeze:

all prompts

all gold labels

evaluation code

analysis scripts

model metadata

provider parameters

Everything goes into:

movable-feast-core/
  data/
  prompts/
  scripts/
  outputs_raw/
  analysis/
  preregistration.md
  README.md

11. Deliverables (v2.2 Core)

Reproducible evaluation dataset

4 prompt conditions

3 movable holidays + 1 fixed

2 languages

25 years + anomaly years

100 samples per condition

Full GLMM inference

Clustering optional (removed from core claims)

This is publishable, tractable, and statistically powered.

🎯 Summary: Why v2.2 Core Works

v2.2:

Removes ambiguity

Eliminates unpowered comparisons

Reduces conditions by 50%

Cuts call volume by 80%

Produces valid statistical inferences

Is feasible to run now

Preserves the essence of Movable Feast

Establishes a solid base for v3.0 expansion
