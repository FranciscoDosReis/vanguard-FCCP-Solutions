# Vanguard A/B Test: Evaluating the New Online Application Process

## 1. Project Overview

**Client / Role:** Director of Product and UX team at Vanguard.

**Business problem:** Vanguard wants to know whether its redesigned online application process performs better than the previous one, and how that conclusion was reached and justified.

**The experiment:** clients were randomly split into two groups.
- **Control:** traditional online process.
- **Test:** new interface.

Both groups went through the same five recorded events: `start`, `step_1`, `step_2`, `step_3`, `confirm`.

**Questions to evaluate and short answers**

| Question | Answer |
|----------|--------|
| Has the user experience improved? | Mostly. More clients finish, fewer quit, and the typical client is faster. Step 1, step 3 and the confirmation page need work. |
| Is the process completed more often? | Yes: 68.0% vs 64.6% (+3.4 pp, p < 0.001), about 900 more completions. |
| Do clients take less time? | The typical client does: median effective time 4:40 vs 4:58 (perfect runs 3:01 vs 3:46). The mean is slightly higher. |
| Fewer navigation errors? | Step repeats excluding confirm fall (48.9% to 44.8%). Repeated confirms rise (6.8% to 19.9%). |
| What improved, what is a bottleneck? | Exits at the start fall. Step 1 and step 3 lose more clients; the confirmation page causes repeats. |
| Deploy the new design? | Yes, in a staged rollout (25% to 50% to 100%), with fixes. |

**Final presentation:** [Google Slides](https://docs.google.com/presentation/d/1wuBYRunZzUmXItrHm8rkECs_bT5D27KTO1fmxMkKjzE/edit)

**Team (FCCP Solutions):** Catarina Macedo, Preeti Tewatia, Charles Payne, Francisco Reis.

## 2. Research Questions

1. Are Test and Control comparable, so that differences can be attributed to the design?
2. Which group has the higher completion rate, and is the difference statistically significant?
3. Where in the five-step funnel do clients stop, and does this differ between groups?
4. Do Test clients complete faster, and make fewer navigation errors?
5. Does age or any other client characteristic influence completion?
6. Is the evidence sufficient to recommend deployment?

## 3. Dataset Description

| File | Content |
|------|---------|
| `df_final_demo` | Client profile: age, gender, tenure, accounts, balance, calls, logons |
| `df_final_web_data` (pt_1, pt_2) | Web events: visit, step, timestamp |
| `df_final_experiment_clients` | Experiment roster (Test / Control) |

**Main variables:** `client_id`, `variation`, `visitor_id`, `visit_id`, `process_step`, `date_time`, `client_tenure_yr`, `client_tenure_mnth`, `client_age`, `gender`, `num_accts`, `total_balance`, `calls_6_mnth`, `logons_6_mnth` (renamed from `clnt_*`, `gendr`, `bal` during cleaning).

### Data quality and cleaning

- Column names lowercased and trimmed; web parts pt_1 and pt_2 merged; files joined on `client_id`.
- 5,934 exact duplicate rows removed (449,831 to 443,897 rows).
- 70,609 clients in the demographics file. **50,500 are in the experiment** (Test 26,968, Control 23,532). 20,109 have no group label and are excluded.
- No client appears in both groups, so each client has a single assignment.
- 13 experiment clients have no age and are left out of age-based analysis. Zero balances are left out of log-scale charts only.
- Repeated steps, backward navigation and multiple sessions are treated as real behaviour, not errors. Out-of-order journeys are kept as a separate category.

### Demographic profile

Analysed on one row per client (not per web event), so each client counts once.

| Variable | Test (mean / median) | Control (mean / median) |
|----------|----------------------|-------------------------|
| Clients | 26,968 | 23,532 |
| Age (years) | 47.17 / 48 | 47.50 / 48 |
| Tenure (years) | 11.98 / 11 | 12.09 / 11 |
| Number of accounts | 2.25 / 2 | 2.26 / 2 |
| Balance | 148,963 / 65,468 | 150,147 / 66,024 |
| Calls (6 months) | 3.06 / 3 | 3.13 / 3 |
| Logons (6 months) | 6.10 / 6 | 6.17 / 6 |

| Gender (%) | Female | Male | Unspecified |
|------------|--------|------|-------------|
| Test | 32.3 | 33.3 | 34.4 |
| Control | 32.1 | 33.9 | 34.1 |

- **Balance** is right-skewed (the mean is more than double the median), so medians and log-scale plots are used. About one third of clients have an unspecified gender.
- **Comparability tests:** Mann-Whitney for numeric variables, chi-squared for gender. With 50,500 clients, small p-values can appear for tiny gaps, so the size of the gap is judged as well.
- **Correlation heat maps** were drawn for each group, with the same colour scale. Similar matrices mean the two groups have the same structure. `client_tenure_yr` and `client_tenure_mnth` are almost the same variable.
- **Gender counts:** female 8,716 Test / 7,543 Control; male 8,977 / 7,970; unspecified 9,275 / 8,019. **Age** is bimodal, with peaks at 25-35 and 50-65.
- **Result:** Test and Control are very similar. Median age, tenure, accounts, calls and logons are identical in both groups; the median balance is 0.8% lower in Test (65,468 vs 66,024); mean age differs by 0.3 years; and no gender share differs by more than 0.6 pp. Differences in completion are therefore more likely due to the design than to who was in each group.

## 4. Methodology

**Exploratory data analysis.** Checked missing values, duplicates, identifiers, timestamps and group assignment, then profiled the demographics of each group (above).

**User journey analysis.** Rebuilt each client's journey across all sessions: which steps were reached, how many times, in what order, and the furthest step reached.

**Performance metrics (per client, all sessions combined)**

| KPI | Definition |
|-----|------------|
| Completed | All 5 steps reached, repeats allowed |
| Perfect completion | All 5 steps, each exactly once |
| Completed with repetitions | All 5 steps, at least one repeated |
| Incomplete | At least one step missing |
| Out of order | Steps not followed in the expected sequence |
| Effective time | Time spent in the process, with gaps over 30 minutes treated as idle |

The client is the unit because the experiment assigns clients, not visits. A missing step is treated as incomplete, so a `confirm` without the earlier steps is not a completion.

**Group comparison.** Rates, funnel and times compared between Test and Control, and completion compared by age group.

**Statistical analysis**
- 2x2 chi-squared test on completion, with Cramer's V as the effect size.
- Chi-squared on the last step reached (observed vs expected counts) to locate where clients stop (chi-squared = 242.2, p < 0.001, Cramer's V = 0.07).
- Assumption: independent clients, one group each. The large sample meets the chi-squared conditions.
- With this many clients, significance is easy to reach, so effect size and business relevance are reported too.

## 5. Key Findings

| KPI | Test | Control | Difference |
|-----|------|---------|-----------|
| Completed | 68.0% (18,338) | 64.6% (15,206) | +3.4 pp |
| Perfect completion | 30.7% (8,273) | 31.3% (7,360) | -0.6 pp |
| Completed with repetitions | 37.3% (10,065) | 33.3% (7,846) | +4.0 pp |
| Incomplete | 32.0% | 35.4% | -3.4 pp |

**Completion.** Test is 3.4 pp higher (chi-squared = 64.2, p < 0.001), about 900 more completions. The effect is small (Cramer's V = 0.04).

**Where clients stop (last step reached)**

| Last step | Control (observed / expected) | Test (observed / expected) | In Test |
|-----------|------------------------------|---------------------------|---------|
| Start | 3,327 / 2,787 | 2,654 / 3,194 | 540 fewer stopped |
| Step 1 | 1,489 / 1,632 | 2,013 / 1,870 | 143 more stopped |
| Step 2 | 1,320 / 1,291 | 1,450 / 1,479 | about the same |
| Step 3 | 2,190 / 2,192 | 2,513 / 2,511 | the same |
| Confirm | 15,206 / 15,631 | 18,338 / 17,913 | 425 more completed |

**Abandonment** (clients who started in order and never reached confirm): 34.26% in Control vs 30.60% in Test. By step (share of abandoners, Control to Test): start 40.75% to 29.74%, step 1 18.00% to 23.90%, step 3 25.69% to 29.35%. Drop-off moves from the start to step 1 and step 3.

**Time.** Median effective time (gaps over 30 minutes treated as idle) is 4:40 in Test vs 4:58 in Control; perfect completions take 3:01 vs 3:46. The mean is slightly higher in Test (7:51 vs 7:36) because a minority of Test clients take much longer.

**Repeated steps.** Excluding confirm, repeats fall from 48.9% to 44.8%. Including confirm, more Test completers repeat a step (54.9% vs 51.6%), because repeated confirms rise from 6.8% to 19.9%. Clients seem to return to check that the submission worked.

**Step 3 and step 1.** Step 3 is the longest step (1:15 to 0:47 in Test) and about 1 in 5 clients go back from it. Backward moves from step 1 almost double (9.6% to 18.0%).

**Tracking gaps.** Missing steps are more frequent in Test (1.29% vs 0.97%), and confirm-only journeys are 193 vs 36.

**Statistical vs practical significance.** The completion gain is statistically significant but small (about 5% relative). Its value depends on the customer base and the rollout cost.

## 6. Conclusions

**What the experiment shows.** The new interface gets more clients through, loses fewer overall, and is faster for the typical client. Friction moves to step 1, step 3 and the confirmation page.

**Statistical support.** The completion difference is significant (p < 0.001) but small (V = 0.04). Test and Control have the same demographic profile, but the unequal split (53.4% vs 46.6%) is unexplained.

**Recommendation.** Launch the new design in a staged rollout (25% to 50% to 100%), and before or during it:
1. Audit the traffic split with engineering.
2. Simplify or pre-fill step 1.
3. Split or pre-fill step 3.
4. Show a clear confirmation message, reference ID and email.
5. Fix event tracking in Test.
6. Validate with usability tests, and compare the roughly 900 extra completions with the rollout cost.

**Limitations**
- Unequal group split: Test is 53.4% vs 46.6% Control, which deviates from 50/50.
- 20,109 clients have no group label and were excluded.
- Tracking gaps in Test; behaviour is observed, not the reasons behind it.
- Analytical choices: 5,934 duplicate events removed, gaps over 30 minutes excluded, repeats treated as friction.
- The test ran from March to June 2017. There is no revenue, migration cost or satisfaction data.

## Appendix

How journeys were rebuilt and time was measured: see the appendix slide and the notebooks in `/notebooks`.

## 7. Next Steps

Describe possible improvements, additional analyses, or future work.

Consider what additional information or investigation could strengthen the conclusions of the experiment.

## 8. Repository Structure

The project follows this directory structure:

```text
├── data/
│   ├── raw/           # Original datasets
│   └── clean/         # Processed datasets
├── notebooks/         # Jupyter notebooks
├── figures/           # Charts and visualizations
├── slides/            # Final presentation or link
├── config.yaml        # Project file paths
├── pyproject.toml     # Python dependencies
└── uv.lock            # Locked dependency versions
```

Each directory contains its own README with additional information about file organization and naming conventions.

## 9. Team Members

| Name | Role | Main Contributions |
|---|---|---|
| ... | Project Manager | ... |
| ... | Team Member | ... |
| ... | Team Member | ... |
