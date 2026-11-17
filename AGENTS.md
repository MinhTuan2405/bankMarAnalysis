# AGENTS.md

## Editing rules (highest priority)

- Always edit the **current file on disk**. Read it first; never regenerate or overwrite a whole file (notebooks included) from a script or an older copy.
- Uncommitted user changes (deletions, edits) are final: never restore, re-add, or rewrite what the user removed or changed.
- Edit notebooks cell by cell, only the cells the task needs.
- Work step by step: do only the steps the user listed, then stop and wait for the next instruction. Never run ahead to later steps/phases.
- Work like a real analyst: do every experiment inside the notebook, one step at a time (add cells → run → read the output). Each decision (which model to tune, ensemble members, threshold…) must come from results already shown in the notebook. No hidden prototype scripts, no assumed results.

## Notebook writing style

- Focus on the work: code + results. No introductory text, no process descriptions, no explaining what a section is about to do.
- Markdown cells: headings only (e.g. `## 1.3 Rename columns`). No paragraphs under headings.
- Explanations, if needed, go in short code comments (one line).
- No pretty printing: no formatted `print(f"...")` messages; let the cell return the raw value (`df.shape`, `df.isna().sum().sum()`).
- Don't beautify output: default prints/plots (no `.round()`, no custom titles, colors, limits, labels, themes). Model evaluation = standard sklearn output: `classification_report` + default `ConfusionMatrixDisplay`, `RocCurveDisplay`, `PrecisionRecallDisplay`.
- Show each piece of information once; don't add cells/tables/charts that repeat what another output already shows.
- No notes/observations in notebooks. Findings go to `analysis/result.md` (one `##` section per EDA phase): short bullets with numbers, only what matters. This file is the basis for the report.
- Language: everything in notebooks in English (markdown, notes, comments, printed text, chart labels).

## Project conventions

- Data: `pd.read_csv(path, sep=";")`; renamed columns via `COLUMN_MAP` (see `analysis/BankMarAnalysis_EDA.ipynb`, step 1.3). Reuse the same map in `analysis/BankMarAnalysis_Dashboard.ipynb` and `predict/`.
- Results / findings: `analysis/result.md`. Prediction plan: `predict/Predict_plan.md`.
- `random_state=42` for anything random.
- Every chart: `save_fig("<section>_<name>")` before `plt.show()` → `analysis/figures/*.png` (for the report). List the files under the matching section in `result.md`.
