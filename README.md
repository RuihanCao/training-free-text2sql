# Training-Free Text-to-SQL (BIRD)

Code and notebooks for the paper **"How Far Can Training-Free Text-to-SQL Go?
An Empirical Study on Corrected BIRD."**

This repository is a **training-free** baseline for Text-to-SQL — no fine-tuning,
no task-specific weights. It pairs an off-the-shelf, 4-bit-quantized **Qwen3-14B**
(`unsloth/Qwen3-14B-bnb-4bit`, served with vLLM) with five inference-time steps and
measures how far that gets on the BIRD benchmark — in particular on a
**human-corrected BIRD subset**, where gold-SQL/label noise is reduced.

## The approach

1. **Schema representation** — databases are rendered as a DDL-style schema
  (precomputed and cached in `bird_schema_cache.json`).
2. **Structured prompting** — a fixed set of 19 natural-language directives that
  constrain how the model writes SQL.
3. **N-best sampling + result-based voting** — N = 4 candidates are sampled and
  voted on by their *execution results*, not their text.
4. **Post-generation value fuzzy matching** — literal values in the SQL are
  reconciled against actual cell values using `rapidfuzz`.
5. **Execution-driven self-correction** — candidates that error or return empty
  are fed back for a correction pass.

Candidate SQL is executed against SQLite with a 5-second timeout, and execution
accuracy (EX) is measured with set-based comparison.

## Repository layout

```
notebooks/
  01_finetuning_baseline/      §4.2 — fine-tuned comparison line (Qwen2.5-Coder LoRA + voting)
  02_training_free_pipeline/   §4.3–4.4, §4.6 — the main training-free approach
  03_component_ablation_5seed/ §4.5 — component ablation across 5 seeds + significance tests
  04_cross_model_transfer/     §4.7 — same approach on Qwen3.5-Plus / Qwen3.5-Coder
data/                          datasets & schema cache (not committed — see data/README.md)
requirements.txt
LICENSE                        MIT
```

## Notebook → paper map


| Notebook                                                           | Paper    | Role                                                                    |
| ------------------------------------------------------------------ | -------- | ----------------------------------------------------------------------- |
| `01_finetuning_baseline/finetune_qwen_completion_only.ipynb`       | §4.2     | LoRA fine-tune (completion-only) of the baseline model                  |
| `01_finetuning_baseline/majority_vote_bird_eval.ipynb`             | §4.2     | Evaluates the fine-tuned model with majority voting                     |
| `02_training_free_pipeline/full_pipeline_heldout.ipynb`            | §4.6     | Full approach on the held-out BIRD dev set (main generalization result) |
| `02_training_free_pipeline/corrected_full_pipeline_main.ipynb`     | §4.4     | Full approach on the corrected BIRD subset (headline configuration)     |
| `02_training_free_pipeline/corrected_baseline_no_directives.ipynb` | §4.3–4.4 | No-directives control / build-up starting point                         |
| `03_component_ablation_5seed/ablation_5seed_full_pipeline.ipynb`   | §4.5     | Full approach, 5 seeds (T=0.6)                                          |
| `03_component_ablation_5seed/ablation_5seed_no_fuzzy.ipynb`        | §4.5     | Same, with fuzzy matching removed (its ablation arm)                    |
| `03_component_ablation_5seed/mcnemar_report.ipynb`                 | §4.5     | McNemar / significance reporting over the 5-seed runs                   |
| `04_cross_model_transfer/qwen35plus_baseline.ipynb`                | §4.7     | Qwen3.5-Plus, baseline (no directives)                                  |
| `04_cross_model_transfer/qwen35plus_full_pipeline.ipynb`           | §4.7     | Qwen3.5-Plus, full approach                                             |
| `04_cross_model_transfer/qwen35coder_full_pipeline.ipynb`          | §4.7     | Qwen3.5-Coder, full approach                                            |


For the exact EX numbers behind each table, see the paper.

## Setup

These are **Google Colab** notebooks — the intended way to run them is to open a
notebook in Colab and **Run all**. The first cell installs the pinned
dependencies (via `uv`), so there's nothing to set up beforehand. You need a GPU
runtime that can serve Qwen3-14B in 4-bit (e.g. a Colab A100).

The cross-model notebooks (`04_`*) call the Qwen3.5 API instead of a local model:
set `QWEN_API_KEY` (and optionally `QWEN_BASE_URL`) via Colab Secrets or an
environment variable.

Then follow [data/README.md](data/README.md) to get the BIRD data and schema
cache, and fill in the placeholder `path/to/...` constants near the top of each
notebook.

## Citation

```bibtex
@misc{trainingfreebird2026,
  title  = {How Far Can Training-Free Text-to-SQL Go? An Empirical Study on Corrected BIRD},
  author = {Ruihan Cao}, {Yifang Luo}, {Fan Zhang}
  year   = {2026},
  note   = {Preprint}
}
```

## License

MIT — see [LICENSE](LICENSE).