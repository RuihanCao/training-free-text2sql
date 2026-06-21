# 03 · Component ablation, 5 seeds (paper §4.5)

Isolates each pipeline component by removing it and re-running across 5 seeds
(temperature 0.6), with significance testing.

- `ablation_5seed_full_pipeline.ipynb` — the full pipeline reference runs.
- `ablation_5seed_no_fuzzy.ipynb` — fuzzy matching removed (one ablation arm).
- `mcnemar_report.ipynb` — McNemar / significance reporting over the seed runs.
