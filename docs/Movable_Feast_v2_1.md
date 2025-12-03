Movable Feast v2.1 — Final Experimental Plan
1. Objectives and Hypotheses

The study evaluates whether large language models (LLMs) can perform calendar-based temporal reasoning for movable holidays across multiple languages and calendar systems. The focus is on distinguishing:

memorization

pattern-triggered behavior

in-context interpolation

generalizable, rule-consistent behavior

To structure analysis, we pre-register four primary hypotheses and one auxiliary hypothesis.

H1 — Behavioral Stratification

Models cluster into distinct behavioral groups when performing movable-holiday recognition under controlled conditions.

Null (H1₀): No statistically significant differences across models after accounting for prompt condition, language, year, and holiday type.

H2 — Temporal Generalization

Models exhibiting near-ceiling performance under baseline conditions maintain performance across:

past years (1990–1999),

middle years (2010–2019),

recent years (2020–2025), and

future years (2030–2040).

Null (H2₀): Accuracy varies significantly across year strata.

H3 — Holiday-Type Difficulty

Movable holidays (lunar-based and Computus-based) produce significantly lower accuracy than fixed-date holidays absent explicit mappings.

Null (H3₀): No difference in accuracy between movable and fixed-date holidays.

H4 — Prompt-Condition Sensitivity

Model performance differs across prompt conditions in the mechanism ladder (e.g., gregorian-only vs examples-only vs CoT-full).

Null (H4₀): Prompt condition does not significantly impact accuracy (difference < 20%).

H5 — Synthetic-Rule Generalization (Auxiliary)

Models cannot generalize novel, synthetic calendar mappings with few-shot examples, revealing whether behavior is algorithmic or associative.

Null (H5₀): Models generalize synthetic rules with accuracy comparable to natural holidays.

2. Factors and Experimental Conditions
2.1. Year Strata

To probe memorization, recency bias, and algorithmic generalization, we sample dates from:

Past: 1990–1999

Middle: 2010–2019

Recent: 2020–2025

Future: 2030–2040

Additionally, we include hard-calendar years, specifically:

Chinese lunisolar anomalies: 2033–2034

Orthodox Easter divergence years

Islamic calendar irregularities (Umm al-Qura deviations)

These serve as stress tests for non-memorized reasoning.

2.2. Holiday Types

We cover five calendar systems:

Lunisolar (East Asian)

Chinese New Year (zh)

Vietnamese Tết (vi)

Korean Seollal (ko)

Mid-Autumn Festival, Dragon Boat Festival (subset)

Christian (Computus)

Western Easter (Gregorian method)

Orthodox Easter (Julian-based method)

Islamic (Umm al-Qura / arithmetic variants)

Eid al-Fitr

Hebrew (lunisolar)

Rosh Hashanah

Hindu (lunisolar)

Diwali (optional for v2.1; planned for v2.2)

Fixed-date controls

Christmas

National Days (CN 10/1, VN 9/2, KR 8/15, US 7/4)

2.3. Languages

Tasks are administered in each holiday’s natural linguistic setting:

Mandarin Chinese (zh)

Vietnamese (vi)

Korean (ko)

English (en)

Language alignment is critical for minimizing cultural bias confounds.

2.4. Prompt Conditions (Mechanism Ladder)

We evaluate distinct reasoning pathways:

gregorian-only

minimal (task framing only)

examples-only

CoT-minimal (instructions only)

CoT-full (instructions + examples)

resolver-correct (oracle mapping injected)

resolver-wrong (contradictory mapping)

partial-resolver (e.g., lunar month/day only)

synthetic-rule few-shot (for exploratory testing of H5)

Each condition is tested independently.

3. Dataset Construction
3.1. Holiday Computation

Holiday dates are computed using deterministic libraries or pure Python implementations:

Chinese/Vietnamese lunisolar: lunardate, lunisolar

Korean lunisolar: korean_lunar_calendar_py

Islamic: ummalqura, arithmetic tabular algorithm

Hebrew: hdate

Western Easter: Butcher's Algorithm (Meeus variant)

Orthodox Easter: Julian Computus

All calendar algorithms are explicitly version-controlled.

3.2. Sampling Strategy

For each model × prompt condition × language × holiday:

Sample 10 years per stratum, totaling 40 years.

Add 5–10 stress-case years for each calendar type.

Per holiday, this yields ~50 test cases per language.

Across 5 holidays × 4 languages × 7 prompt conditions:

→ Approximately 700–900 test queries per model (manageable and statistically meaningful).

3.3. Negative Controls

Negatives include:

Random non-holiday dates

Adjacent dates (±1, ±2 days)

Impossible dates (Feb 30, Apr 31)

Off-calendar mappings (e.g., asking for Easter for a Chinese date)

Negatives use equal sampling to positive cases to avoid class imbalance.

4. Models and Providers
4.1. Models (Real, Stable, Current)

OpenAI: GPT-4o, o1-preview

Anthropic: Claude 3.5 Sonnet, Opus

Google: Gemini 1.5 Pro

xAI: Grok-2 (reasoning/fast variants)

Meta: Llama 3.1 70B

Alibaba: Qwen 2.5 72B

Mistral: Mistral Large

DeepSeek-V3 (if available)

4.2. Provider Ablation

Each model is evaluated across:

Native official API

OpenRouter or Together (secondary provider)

Tool access must be disabled.
Provider-level metadata and response headers are logged.

5. Prompt Templates

Each condition uses a standardized, language-aligned prompt with strict:

JSON-only output

No chain-of-thought unless explicitly requested

No external knowledge beyond prompt

Resolver precedence rules

Controlled example variants

Deterministic phrasing across languages

Templates are version-controlled and stored in the repo.

6. Metrics

We compute:

Accuracy

Precision / Recall / F1

False Positive Rate (FPR)

Refusal Rate

Holiday confusion matrices

Year-stratum deltas

Prompt-condition deltas

Stability across providers

Confidence intervals:

Bootstrap 95%

Clopper–Pearson for proportions

Inferential tests:

χ² or Fisher for group comparisons

Mixed-effects logistic regression with random intercepts for model, language, holiday, and year

Multiple-comparisons correction (Benjamini–Hochberg)

7. Controls and Validity Checks

Disable tool-use

Record model version IDs and server timestamps

Strict JSON schema enforcement

Format perturbation testing (diacritics, scripts, ambiguous formats, multilingual mix)

Counterfactual year-shift tests (±19 years for Metonic cycle)

Synthetic calendar generalization tests (H5)

Resolver-wrong contradiction robustness tests

8. Reproducibility and Release

We release:

Repository Structure
movable-feast/
  data/                     # Computed holiday datasets
  prompts/                  # All prompt templates
  scripts/                  # run_eval.py, analyze_results.py
  results_raw/              # JSONL responses
  configs/                  # provider and model configs
  analysis/                 # notebooks, plots
  README.md                 # run instructions
  LICENSE                   # MIT or CC-BY-4.0

Hugging Face Dataset

Test splits frozen pre-evaluation

Metadata: language, holiday, year, calendar system, difficulty labels

Colab Notebook

Demonstrates full evaluation pipeline

Pre-registration Document

Hypotheses, sampling plan, metrics, and statistical tests are frozen before evaluation.
