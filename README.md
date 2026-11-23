# Vanguard A/B Test: Digital Experience Evaluation

## 1. Project Overview

FCCP Solutions evaluates whether Vanguard should implement its redesigned online application process. For Vanguard’s Product and UX teams, we compare the **Control group** (original interface) with the **Test group** (new interface), focusing on completion, active time and journey friction.

The five recorded steps are `start`, `step_1`, `step_2`, `step_3` and `confirm`.

**Final Presentation:** [View Google Slides](https://docs.google.com/presentation/d/1wuBYRunZzUmXItrHm8rkECs_bT5D27KTO1fmxMkKjzE/edit?slide=id.p1#slide=id.p1)

**Tableau Dashboard:** [View interactive demographic dashboard](https://public.tableau.com/app/profile/preeti.tewatia/viz/vangauard_demographic_visualisation/Demographic_profiles?publish=yes)

## 2. Research Questions

- Are the groups demographically comparable?
- Does the new interface improve completion and reduce abandonment?
- How do active time, repetitions and drop-off locations change?
- Do improvements vary by age, and is the evidence sufficient to support rollout?

## 3. Dataset Description

The Vanguard digital experiment covers **March–June 2017**. Four source dataframes supply client profiles, web events and experiment assignments:

| Source | Content |
|---|---|
| `df_final_demo` | Age, gender, tenure, accounts, balance, calls and logons |
| `df_final_web_data` — pt_1 | Client, visitor, visit, process step and timestamp |
| `df_final_web_data` — pt_2 | Additional web events with the same structure |
| `df_final_experiment_clients` | Client assignment to Test or Control |

Web parts are combined and linked to profiles and assignments through `client_id`. The merged data contains **70,609 unique clients**. We analyse **50,500 experiment clients**: **26,968 Test** and **23,532 Control**; **20,109 unassigned clients** are excluded.

### Main Data Quality Issues

Exact duplicates, missing demographics, repeated or out-of-order steps, incomplete journeys and long idle gaps require different treatment. Thirteen experiment clients have missing age. Balance is strongly right-skewed, and approximately one third of clients have unspecified gender.

### Data Cleaning and Preparation

All members contributed to cleaning the four source dataframes before merging. Preparation standardizes column names and removes **5,934 exact duplicate records**, leaving **443,897 event rows**. Demographics use one row per client to avoid overcounting frequent visitors. Missing ages are excluded only from age-based analysis, and non-positive balances from log-scale charts.

Repeated steps and backward navigation are retained as behavior to investigate. Out-of-order journeys are analysed separately.

## 4. Methodology

- **EDA:** Compare demographic medians, distributions, gender percentages and correlation heatmaps; use Mann–Whitney tests for numeric profile comparisons.
- **Journey analysis:** Examine recorded step coverage, repetitions, navigation order and stopping points across sessions.
- **Group comparison:** Compare rates and effective durations overall and by age.
- **Statistics:** Apply chi-squared tests to completion and step progression, reporting Cramér’s V alongside p-values. Comparisons treat clients, rather than visits, as independent units.

| KPI | Definition |
|---|---|
| Completion | All five steps recorded for a client across sessions, repeats allowed |
| Perfect completion | All five steps recorded exactly once |
| Completion with repetitions | All five steps recorded, at least one repeated |
| Incomplete | At least one required step missing |
| Abandonment | Client starts in order and never reaches confirmation |
| Effective time | Active duration excluding gaps longer than 30 minutes |

Completion checks step coverage, not sequence. Completion and abandonment use different journey criteria and are not exact complements. Process-step repetitions exclude confirmation, which is analysed separately.

**Tools:** Python, pandas, NumPy, SciPy, statsmodels, Matplotlib, Seaborn and Tableau.

## 5. Key Findings

| Metric | Control | Test |
|---|---:|---:|
| Completion rate | 64.6% | 68.0% |
| Completed clients | 15,206 | 18,338 |
| Abandonment rate | 34.26% | 30.60% |
| Median effective duration | 4m 58s | 4m 40s |
| Mean effective duration | 7m 36s | 7m 51s |
| Process-step repetitions, excluding confirmation | 48.9% | 44.8% |

Completion improves by approximately **3.4 percentage points** (**χ² = 64.2, p < 0.001**), although the association is small (**Cramér’s V = 0.04**). The groups have similar demographic profiles, and all reported age cohorts improve.

The typical client is faster, but the higher Test mean shows that improvement is not uniform. Steps **1 and 3** remain friction points. Confirmation-page repetitions increase from **6.80% to 19.85%**, suggesting a need for clearer submission feedback.

## 6. Conclusions

**We recommend a staged rollout of the new platform alongside targeted UX improvements.** More clients finish and fewer abandon the process. Statistical significance supports the completion uplift; business value depends on implementation costs and the value of additional completions.

Limitations include historical data, missing demographic values and imperfect event sequences. Logs show behavior without establishing its cause. Idle-time thresholds and KPI definitions affect interpretation; revenue and satisfaction outcomes are not measured here.

## 7. Next Steps

- Simplify or pre-fill step 1 and shorten or pre-fill step 3; validate changes through usability testing.
- Add clear confirmation messages, reference IDs and email acknowledgements.
- Audit event tracking and session timeouts; check the observed **53.4% / 46.6%** cohort split against the intended allocation.
- Monitor rollout KPIs, assess financial benefits and verify age-bin labels against their boundaries.

## 8. Repository Structure

The supplied template organizes the repository as follows:

| Path | Contents |
|---|---|
├── config.yaml          # Path configurations
├── data/
│   ├── raw/             # Raw source CSVs
│   └── clean/           # Prepared datasets
├── figures/             # Output charts and exported plots
├── notebooks/           # Jupyter notebooks (Cleaning, EDA, Hypotheses)
├── pyproject.toml       # Project metadata & dependencies
├── slides/              # Presentation decks
└── uv.lock              # Lockfile for reproducible environment

## 9. Team Members

**FCCP Solutions**

| Name | Role | Main Contributions |
|---|---|---|
| Francisco Reis | Project Manager | Project coordination, preparation of analysis tables, time analysis and creation of the final presentation |
| Catarina Macedo | Team Member | Step-level analysis of the user journey and creation of the final presentation |
| Preeti Tewatia | Team Member | Demographic analysis, Tableau dashboard and README updates |
| Charles Payne | Team Member | Statistical analysis, hypothesis testing and creation of the final presentation |

**Shared contribution:** All members performed basic cleaning of the four source dataframes before merging.
