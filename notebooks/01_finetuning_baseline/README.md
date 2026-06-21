# 01 · Fine-tuning baseline (paper §4.2)

The fine-tuned comparison line: a LoRA fine-tune of the baseline model plus a
majority-voting evaluation. This is the *contrast* to the training-free pipeline,
not part of it.

- `finetune_qwen_completion_only.ipynb` — completion-only LoRA fine-tune.
- `majority_vote_bird_eval.ipynb` — evaluates the fine-tuned model with voting.

Needs the BIRD train databases + training data (see `../../data/README.md`).
