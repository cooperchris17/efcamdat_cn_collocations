# Replication Note: BNC Collocation Dataset

## Summary

The association measure (AM) values in the published dataset (`bnc_collocations_ame_AMs.csv`) differ very slightly from those produced by re-running the shared replication code. This note explains why and confirms that the discrepancy does not impact the study's results.

## Cause

The BNC dataset contains British and American spelling variants (e.g., "organisation"/"organization", "judgement"/"judgment") which are standardised to American English for compatibility with WordNet. This standardisation creates duplicate (w1, w2) pairs whose rows must be merged.

The original analysis was conducted in a Jupyter notebook. Due to cell execution order, the spelling standardisation step was likely inadvertently applied to an already-standardised dataframe during one session. This resulted in fewer duplicate pairs being identified and merged, leaving some cooccurrence values unmerged.

## Impact

The total cooccurrence sum across the dataset differs between the two versions:

| Version | Total cooccurrence |
|---|---|
| Published | 4,630,719 |
| Corrected (replication code) | 4,633,083 |

Because all association measures use this total as the denominator for expected frequency calculations, every row is affected — but uniformly and negligibly. The difference in log odds ratio values is approximately ±0.0005 across all collocations. For example:

| Collocation | Published log OR | Corrected log OR | Difference |
|---|---|---|---|
| *take — place* | 3.9647 | 3.9642 | 0.0005 |
| *have — effect* | 2.2204 | 2.2199 | 0.0005 |
| *have — time* | 1.0714 | 1.0709 | 0.0005 |

## Reproducing the Results

The shared replication notebooks (`1_ame_uk_spelling.ipynb` + `2_association_measures.ipynb`) produce the corrected values. To avoid the notebook execution order issue, the script reloads the raw data immediately before the spelling standardisation step:

```python
# Reload fresh data before standardising (prevents double-application)
bnc_df = bnc_df_input.dropna(subset=['w1', 'w2']).copy()

bnc_df.loc[:, 'w1'] = bnc_df['w1'].apply(standardize_word_to_american)
bnc_df.loc[:, 'w2'] = bnc_df['w2'].apply(standardize_word_to_american)
```

## Files

- `bnc_collocations_ame_AMs.csv` — The dataset used in the published study
- `1_ame_uk_spelling.ipynb` + `2_association_measures.ipynb` — Corrected replication notebooks (produces marginally different AM values as described above)
