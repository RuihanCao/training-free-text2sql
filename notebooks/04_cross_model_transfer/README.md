# 04 · Cross-model transfer (paper §4.7)

The same training-free pipeline applied to other models (Qwen3.5-Plus and
Qwen3.5-Coder) to test whether the gains transfer.

- `qwen35plus_baseline.ipynb` — Qwen3.5-Plus, no directives.
- `qwen35plus_full_pipeline.ipynb` — Qwen3.5-Plus, full pipeline.
- `qwen35coder_full_pipeline.ipynb` — Qwen3.5-Coder, full pipeline.

These call the Qwen3.5 **API**, so they need an API key. Set it via an
environment variable — never hardcode it in the notebook.
