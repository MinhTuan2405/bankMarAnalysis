# Bank Marketing Analysis

A repository for exploring bank telemarketing campaign data and predicting whether a client will subscribe to a term deposit. It currently contains the source datasets; analysis and prediction code have not yet been added.

## Dataset Introduction

The [Bank Marketing dataset](https://archive.ics.uci.edu/dataset/222/bank+marketing), hosted by the UCI Machine Learning Repository, describes direct marketing campaigns conducted by a Portuguese banking institution. Campaigns used telephone calls to offer bank term deposits, and a client could be contacted more than once before the subscription outcome was recorded.

The machine learning task is **binary classification**: predict the target variable `y`, where `yes` means that the client subscribed to a term deposit and `no` means that the client did not. The data combines client characteristics, information about the current campaign's last contact, and previous campaign history. The additional version also includes social and economic indicators.

### Dataset Overview

| Property | Description |
| --- | --- |
| Source | UCI Machine Learning Repository, dataset ID `222` |
| Domain | Banking, business, and direct marketing |
| Task | Predict term-deposit subscription (`y`) |
| Data characteristics | Multivariate tabular data with categorical and numeric inputs |
| Creators | Sergio Moro, Paulo Cortez, and Paulo Rita |
| Campaign period | May 2008 to November 2010, as documented for the full files |
| Original version | 45,211 records with 16 input features and 1 target column |
| Additional version | 41,188 records with 20 input features and 1 target column |
| File format | Semicolon-separated CSV files with a header row and quoted categorical values |
| License | [Creative Commons Attribution 4.0 International (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/) |
| Dataset DOI | [10.24432/C5K306](https://doi.org/10.24432/C5K306) |

### Available Dataset Files

All four CSV files are included in this repository. Input counts below exclude the target `y`; total column counts include it.

| File | Records | Input Features | Total Columns | Description |
| --- | ---: | ---: | ---: | --- |
| [`bank-full.csv`](dataset/bank/bank-full.csv) | 45,211 | 16 | 17 | Full original version, ordered by date |
| [`bank.csv`](dataset/bank/bank.csv) | 4,521 | 16 | 17 | Approximately 10% random sample of `bank-full.csv` |
| [`bank-additional-full.csv`](dataset/bank-additional/bank-additional/bank-additional-full.csv) | 41,188 | 20 | 21 | Full additional version with social and economic context, ordered by date |
| [`bank-additional.csv`](dataset/bank-additional/bank-additional/bank-additional.csv) | 4,119 | 20 | 21 | Approximately 10% random sample of `bank-additional-full.csv` |

The sample files are intended for experiments with more computationally demanding algorithms. They are subsets of their corresponding full files, not independent test sets. The original and additional versions have different schemas and record counts and should not be concatenated as if they were interchangeable datasets.

### Original Version Data Dictionary

The following schema applies to `bank-full.csv` and `bank.csv`.

| Group | Variable | Type | Meaning and Values |
| --- | --- | --- | --- |
| Client | `age` | Numeric | Client age in years |
| Client | `job` | Categorical | Occupation: `admin.`, `blue-collar`, `entrepreneur`, `housemaid`, `management`, `retired`, `self-employed`, `services`, `student`, `technician`, `unemployed`, `unknown` |
| Client | `marital` | Categorical | `married`, `divorced`, `single`; `divorced` also includes widowed clients |
| Client | `education` | Categorical | `primary`, `secondary`, `tertiary`, `unknown` |
| Client | `default` | Binary | Whether the client has credit in default: `yes`, `no` |
| Client | `balance` | Numeric | Average yearly account balance, in euros |
| Client | `housing` | Binary | Whether the client has a housing loan: `yes`, `no` |
| Client | `loan` | Binary | Whether the client has a personal loan: `yes`, `no` |
| Last contact | `contact` | Categorical | Communication type: `cellular`, `telephone`, `unknown` |
| Last contact | `day` | Numeric | Day of the month of the last contact |
| Last contact | `month` | Categorical | Month of the last contact, encoded as `jan` through `dec` |
| Last contact | `duration` | Numeric | Duration of the last contact, in seconds; unavailable before the call |
| Campaign history | `campaign` | Numeric | Contacts made during the current campaign for this client, including the last contact |
| Campaign history | `pdays` | Numeric | Days since the client was contacted in a previous campaign; `-1` means no previous contact |
| Campaign history | `previous` | Numeric | Contacts made for this client before the current campaign |
| Campaign history | `poutcome` | Categorical | Previous campaign outcome: `failure`, `success`, `other`, `unknown` |
| Target | `y` | Binary | Whether the client subscribed to a term deposit: `yes`, `no` |

### Additional Version Data Dictionary

The following schema applies to `bank-additional-full.csv` and `bank-additional.csv`. This version removes `balance` and `day`, introduces `day_of_week` and five economic indicators, and changes several category definitions.

| Group | Variable | Type | Meaning and Values |
| --- | --- | --- | --- |
| Client | `age` | Numeric | Client age in years |
| Client | `job` | Categorical | Occupation: `admin.`, `blue-collar`, `entrepreneur`, `housemaid`, `management`, `retired`, `self-employed`, `services`, `student`, `technician`, `unemployed`, `unknown` |
| Client | `marital` | Categorical | `divorced`, `married`, `single`, `unknown`; `divorced` also includes widowed clients |
| Client | `education` | Categorical | `basic.4y`, `basic.6y`, `basic.9y`, `high.school`, `illiterate`, `professional.course`, `university.degree`, `unknown` |
| Client | `default` | Categorical | Whether the client has credit in default: `yes`, `no`, `unknown` |
| Client | `housing` | Categorical | Whether the client has a housing loan: `yes`, `no`, `unknown` |
| Client | `loan` | Categorical | Whether the client has a personal loan: `yes`, `no`, `unknown` |
| Last contact | `contact` | Categorical | Communication type: `cellular`, `telephone` |
| Last contact | `month` | Categorical | Month of the last contact, encoded as `jan` through `dec` |
| Last contact | `day_of_week` | Categorical | Weekday of the last contact: `mon`, `tue`, `wed`, `thu`, `fri` |
| Last contact | `duration` | Numeric | Duration of the last contact, in seconds; unavailable before the call |
| Campaign history | `campaign` | Numeric | Contacts made during the current campaign for this client, including the last contact |
| Campaign history | `pdays` | Numeric | Days since the client was contacted in a previous campaign; `999` means no previous contact |
| Campaign history | `previous` | Numeric | Contacts made for this client before the current campaign |
| Campaign history | `poutcome` | Categorical | Previous campaign outcome: `failure`, `nonexistent`, `success` |
| Economic context | `emp.var.rate` | Numeric | Employment variation rate, a quarterly indicator |
| Economic context | `cons.price.idx` | Numeric | Consumer price index, a monthly indicator |
| Economic context | `cons.conf.idx` | Numeric | Consumer confidence index, a monthly indicator |
| Economic context | `euribor3m` | Numeric | Three-month Euribor interest rate, a daily indicator |
| Economic context | `nr.employed` | Numeric | Number of employees, a quarterly indicator |
| Target | `y` | Binary | Whether the client subscribed to a term deposit: `yes`, `no` |

### Target Distribution

These counts were verified directly from the CSV files in this repository. Subscription rate is the percentage of records with `y = yes`, rounded to two decimal places.

| File | `yes` | `no` | Subscription Rate |
| --- | ---: | ---: | ---: |
| `bank-full.csv` | 5,289 | 39,922 | 11.70% |
| `bank.csv` | 521 | 4,000 | 11.52% |
| `bank-additional-full.csv` | 4,640 | 36,548 | 11.27% |
| `bank-additional.csv` | 451 | 3,668 | 10.95% |

### Data Quality and Modeling Notes

- **Unknown categories:** The original documentation reports no missing attribute values, but labels such as `unknown` occur in the data. The additional documentation explicitly identifies `unknown` as the encoding for missing categorical information. Decide whether to retain it as a category or handle it as missing data; do not assume that the absence of blank cells means all values are known.
- **Different sentinel values:** `pdays = -1` in the original version and `pdays = 999` in the additional version mean that there was no previous contact. These values should not be interpreted as ordinary elapsed days.
- **Pre-call leakage:** Exclude `duration` when building a model intended to select clients before a call. Its value is only known after the call and strongly reflects the outcome; the source documentation recommends it only for benchmark purposes.
- **Class imbalance:** Most outcomes are `no`. Evaluate positive-class precision, recall, F1, and PR-AUC alongside other metrics rather than relying on accuracy alone.
- **Evaluation split:** The full files are ordered by date, so a chronological holdout is appropriate when evaluating future-campaign performance. Do not use the supplied sample as a test set against its full parent file because their records overlap.
- **Loading:** Use a semicolon delimiter, for example `pandas.read_csv(path, sep=";")`, rather than the default comma delimiter.

## Folder Structure

```text
bankmarketinganalysis/
|-- README.md
|-- analysis/                              # Empty local directory for analysis work
|-- dataset/
|   |-- bank/
|   |   |-- bank-full.csv                   # Original full dataset
|   |   |-- bank.csv                        # Original random sample
|   |   `-- bank-names.txt                  # Original dataset documentation
|   `-- bank-additional/
|       |-- bank-additional/
|       |   |-- bank-additional-full.csv    # Additional full dataset
|       |   |-- bank-additional.csv         # Additional random sample
|       |   |-- bank-additional-names.txt   # Additional dataset documentation
|       |   |-- .DS_Store                   # Bundled macOS metadata
|       |   `-- .Rhistory                   # Bundled R session history
|       `-- __MACOSX/                       # Bundled macOS archive metadata
|           |-- ._bank-additional
|           `-- bank-additional/
|               `-- ._.DS_Store
`-- predict/                               # Empty local directory for prediction work
```

`analysis/` and `predict/` currently exist only in the local workspace. Git does not track empty directories, so they will not appear in a fresh clone until files are added. The nested `dataset/bank-additional/bank-additional/` path reflects the current extracted archive layout. The archive metadata and session-history files are not dataset inputs.

## References

- **Dataset citation:** Moro, S., Rita, P., & Cortez, P. (2014). *Bank Marketing* [Dataset]. UCI Machine Learning Repository. https://doi.org/10.24432/C5K306.
- **Additional-version study:** Moro, S., Cortez, P., & Rita, P. (2014). *A Data-Driven Approach to Predict the Success of Bank Telemarketing*. Decision Support Systems, 62, 22-31. https://doi.org/10.1016/j.dss.2014.03.001.
- **Original-version study:** Moro, S., Laureano, R., & Cortez, P. (2011). *Using Data Mining for Bank Direct Marketing: An Application of the CRISP-DM Methodology*. Proceedings of the European Simulation and Modelling Conference, 117-121. http://hdl.handle.net/1822/14838.
- **Local documentation:** [`bank-names.txt`](dataset/bank/bank-names.txt) and [`bank-additional-names.txt`](dataset/bank-additional/bank-additional/bank-additional-names.txt).
