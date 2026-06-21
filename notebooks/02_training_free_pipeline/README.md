# 02 · Training-free pipeline (paper §4.3–4.4, §4.6)

The core method: Qwen3-14B (4-bit) + DDL schema + 19 directives + N=4 result
voting + fuzzy matching + execution-driven self-correction. No fine-tuning.

- `full_pipeline_heldout.ipynb` — full pipeline on the held-out BIRD dev set (§4.6).
- `corrected_full_pipeline_main.ipynb` — full pipeline on the corrected BIRD subset; the headline configuration (§4.4).
- `corrected_baseline_no_directives.ipynb` — no-directives control and build-up starting point (§4.3–4.4).
