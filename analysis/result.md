# EDA Results — `bank-full.csv`

Source: `analysis/BankMarAnalysis_EDA.ipynb` (section numbers refer to the notebook). Figures: `analysis/figures/` (PNG, 200 dpi, file name = notebook section).

## 2. Data Quality

Figures: `2.6_outliers_boxplot.png`

- No NaN; missing is encoded as `unknown`: `prev_campaign_outcome` 81.7%, `contact_type` 28.8%, `education` 4.1%, `job` 0.6%.
- No duplicates (also when ignoring the target).
- No out-of-range values. Unusual but valid: `last_contact_duration = 0` (3 rows, all target 0), `avg_yearly_balance < 0` (3,766 rows, 8.3%).
- `days_since_prev_campaign = -1` ⇔ `n_contacts_before_campaign = 0` (36,954 rows = never contacted); `prev_campaign_outcome = unknown` = these rows + 5 contacted rows → only 5 truly missing.
- IQR outliers: `avg_yearly_balance` 10.5%, `last_contact_duration` 7.2%, `n_contacts_this_campaign` 6.8%, `age` 1.1%. For `days_since_prev_campaign` / `n_contacts_before_campaign` IQR = 0 (81.7% at -1 / 0), so IQR flags every contacted client → not meaningful.

## 3. Univariate Analysis

Figures: `3.1_numerical_distributions.png`, `3.2_calls_by_month_and_day.png`, `3.3_categorical_counts.png`

- Strong right skew: `n_contacts_before_campaign` (skew 41.9, max 275), `avg_yearly_balance` (8.4), `n_contacts_this_campaign` (4.9), `last_contact_duration` (3.1); `age` near symmetric (0.7).
- `days_since_prev_campaign`: 81.7% = -1; contacted clients median 194 days.
- Calls concentrated in May (30.4%), then Jul/Aug/Jun; Mar/Sep/Oct/Dec < 2% each.
- Rare levels (< 2%): `job = unknown`, `has_credit_default = 1` (1.8%), months oct/sep/mar/dec.
- Target: 11.7% subscribed.

## 4. Bivariate Analysis

Figures: `4.1_numerical_pairs_scatter.png`, `4.2_numerical_by_category.png`, `4.3_job_vs_education.png`, `4.3_contact_type_by_month.png`

- `days_since_prev_campaign` – `n_contacts_before_campaign`: Spearman 0.99 (both encode "contacted before"); other numeric pairs ≈ 0.
- `job` – `education` strongly linked: management 82% tertiary, admin./services > 80% secondary, housemaid 51% primary.
- Age by job: retired median 59, student 26, others 37–47. Balance medians 340–790, highest for retired/management.
- `contact_type = unknown` is almost only May (58%) and Jun (85%) → recording issue at the start of the data.

## 5. Multivariate Analysis

Figures: `5.1_correlation_heatmap.png`, `5.2_pairplot.png`

- No multicollinearity: all VIF ≤ 1.3.
- Spearman with target: `last_contact_duration` 0.34, `n_contacts_before_campaign` 0.17, `days_since_prev_campaign` 0.15, `has_housing_loan` −0.14; others ≤ 0.10.
- Mar/Sep/Oct/Dec have high rates on cellular/telephone (38–53%); May is lowest on every channel (3–12%).
- Age 60+ subscribes at 30–71% in almost every job; students 20–31%.

## 6. Target-Oriented EDA

Figures: `6.2_subscription_rate_by_category.png`, `6.3_numerical_by_target.png`, `6.3_subscription_rate_by_bins.png`, `6.5_time_drift.png`

- Imbalance 7.5 : 1.
- Strongest categoricals (Cramér's V): `prev_campaign_outcome` 0.31 (success 64.7% vs 9–17%), `last_contact_month` 0.26 (mar/sep/oct/dec 44–52%, may 6.7%), `contact_type` 0.15 (unknown 4.1% vs 13–15%), `has_housing_loan` 0.14 (7.7% vs 16.7%), `job` 0.14 (student 28.7%, retired 22.8%, blue-collar 7.3%). Weakest: `has_credit_default` 0.02.
- Subscribers: higher balance (median 733 vs 417), longer calls (426 vs 164 s). `age` medians equal but rate is U-shaped: ≤ 30 16.3%, 30–60 9–10%, 60+ 42.3%.
- Rate falls with `n_contacts_this_campaign`: 14.6% at 1 call → 4.2% at 10+.
- Mutual information: `last_contact_duration` ≫ `prev_campaign_outcome`, `days_since_prev_campaign`, `last_contact_month`, `avg_yearly_balance`.
- Strong time drift: rate 3.4% in the first 20% of rows → 31.6% in the last 20% (~50% at the end).
- Leakage confirmed: rate rises from 0.2% (shortest decile of `last_contact_duration`) to 45.5% (longest).

## 7. Insights & Recommendations

### 7.1 Findings

- Quality: no NaN, duplicates or invalid values; missing is hidden as `unknown` (`prev_campaign_outcome` 81.7%, `contact_type` 28.8%, `education` 4.1%, `job` 0.6%).
- `days_since_prev_campaign = -1` / `n_contacts_before_campaign = 0` / `prev_campaign_outcome = unknown` all mean "never contacted" (81.7%).
- Skewed with long tails: `avg_yearly_balance`, `last_contact_duration`, `n_contacts_this_campaign`, `n_contacts_before_campaign`.
- Target imbalanced: 11.7% subscribed.
- Main drivers: previous success, month, contact type, housing loan, job/age (students, retired, 60+), balance, fewer calls.
- Rows are time-ordered and the subscription rate drifts from ~3% to ~50%.
- `last_contact_duration` is leakage.

### 7.2 Recommendations for modeling

- Drop `last_contact_duration`.
- Keep `unknown` as its own category (informative for `contact_type`, `prev_campaign_outcome`).
- Add flag `was_contacted_before = days_since_prev_campaign != -1`; treat -1 separately.
- Keep outliers (valid values); log / clip `avg_yearly_balance`, `n_contacts_this_campaign`, `n_contacts_before_campaign` for linear models.
- One-hot encode categoricals incl. `last_contact_month` (all ≤ 12 levels).
- Imbalance: stratified split, class weights, evaluate with PR-AUC / F1 / recall.
- Time drift: validate with a time-based split (train on earlier rows, test on later) besides random split.

## Dashboard — What drives term-deposit subscription?

Source: `analysis/BankMarAnalysis_Dashboard.ipynb`. Figures: `dashboard_1_factor_ranking.png`, `dashboard_2_client_factors.png`, `dashboard_3_campaign_factors.png`, `dashboard_4_key_segments.png`

- Overall subscription rate: 11.7%. `last_contact_duration` excluded (leakage).
- Factor ranking (Cramér's V): `prev_campaign_outcome` 0.31 > `last_contact_month` 0.26 > `age_group` 0.17 ≈ `was_contacted_before` 0.17 > `contact_type` 0.15 > `has_housing_loan` 0.14 ≈ `job` 0.14 > `balance_group` 0.10 > `calls_group` 0.09 > `education`, `has_personal_loan`, `marital` 0.07 > `has_credit_default` 0.02.
- Campaign: previous success 64.7%; contacted before 23.1% vs 9.2%; Mar/Sep/Oct/Dec 44–52% vs May 6.7%; cellular 14.9% vs unknown 4.1%; 1 call 14.6% → 11+ calls 3.9%.
- Client: age 61+ 42.3%, 18–30 16.3%, 31–60 9–10%; student 28.7%, retired 22.8%, blue-collar 7.3%; balance Q5 16.4% vs Q1 7.0%; no housing loan 16.7% vs 7.7%; no personal loan 12.7% vs 6.7%; tertiary 15.0% vs primary 8.6%.
- Segments: age 61+ subscribes at 33–45% in every balance group; young + high balance (18–30, Q5) 24%. Previous success stays high (41–70%) even after several calls; for other clients the rate drops as calls increase.
