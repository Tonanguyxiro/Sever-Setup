# Evaluation

The `evaluation/` module orchestrates baselines and benchmarks to produce experimental results. Layout:

- `baseline/` — model wrappers used by every eval entry point.
- `benckmark/` — datasets and dataset-handling helpers (note: folder name keeps the historical typo).
- `eval_<topic>_<method>_<extra>.py` — evaluation entry points.
- `results-<mode>/` — output directories, one per evaluation mode (e.g. `results-accuracy/`, `results-speculative/`).

## Import And Run Rules

- `evaluation/` must be imported as a package.
- Inside `evaluation/`, use absolute package imports:
  - `from evaluation.baseline import ...`
  - `from evaluation.benckmark import ...`
- Do not append to `sys.path` in eval entries, baseline wrappers, or benchmark handlers.
- Do not import `evaluation/` submodules from outside `evaluation/`.
- Always run from the repo root:
  - `uv run evaluation/eval_<...>.py`
  - `uv run -m evaluation.eval_<...>`
- Do not assume scripts can be launched from inside the `evaluation/` subdirectory.

## `baseline/` — Model Wrappers

Each baseline is a class with a unified interface. Two base classes are expected:

- `LocalModelBase` — exposes `generate(prompt, ...) -> dict` for one-shot generation (used by accuracy-style evals).
- `DraftModelBase` — exposes the speculative-decoding step interface:
  - `prefill(...)` — initialize state from a prompt.
  - `draft(...)` — propose `draft_len` tokens from current state.
  - `rollback(...)` — undo rejected draft tokens.
  - `accept(...)` — commit accepted tokens (and optionally a corrected token) into state.
  - `finalize(...)` — clean up at end of generation.
  - `sd_generate(...)` — full speculative-decoding loop stimulation (using target model to generate reference ahead).

Concrete wrappers (e.g. `LocalHFModel`, `MyGistDraftModel`, …) are exported from `evaluation/baseline/__init__.py`.

Responsibilities:

- A baseline handles only: model loading, prompt preprocessing, generation/draft calls, and timing.
- A baseline does **not** handle benchmark data loading, metric computation, or result aggregation.

## `benckmark/` — Datasets

Datasets live under `evaluation/benckmark/data/<dataset_name>/*.jsonl`. Filenames encode context length, e.g. `*_4K.jsonl`, `*_16K.jsonl`. Eval scripts discover them by globbing and parsing the length suffix.

Optional helpers (when present) are exported from `evaluation/benckmark/__init__.py`, e.g.:

- `EvalSample`, `BenchmarkTask`
- `BenchmarkRunner`, `BenchmarkResultWriter`
- `get_benchmark_handler(...)`, `discover_longbench_tasks(...)`, `discover_helmet_tasks(...)`

Responsibilities:

- A benchmark handler handles: sample construction, post-processing, scoring, and result writing.
- A benchmark handler does **not** drive the main eval loop.

## `eval_<...>.py` — Entry Points

Naming: `eval_<topic>_<method>_<extra>.py` (e.g. `eval_spec_main_longin_v2.py`). The `<extra>` slot typically encodes a variant or version (`longin`, `longout`, `v2`, …).

Recommended structure:

```python
from __future__ import annotations

import argparse
from datetime import datetime
from pathlib import Path
from typing import Any

import torch
from tqdm import tqdm

from evaluation.baseline import ...

# 1. Defaults
DATA_ROOT = Path("~/<repo>/evaluation/benckmark/data").expanduser()
RESULTS_ROOT = Path("~/<repo>/evaluation/results-<mode>").expanduser()

# 2. CLI
def parse_args() -> argparse.Namespace:
    parser = argparse.ArgumentParser()
    parser.add_argument("--data-root", type=Path, default=DATA_ROOT)
    parser.add_argument("--results-root", type=Path, default=RESULTS_ROOT)
    # ... per-field CLI overrides ...
    return parser.parse_args()


# 3. Merge preset + CLI overrides into a single config dict
def resolve_config(args: argparse.Namespace) -> dict[str, Any]:
    ...


# 4. Pipeline
def main() -> None:
    args = parse_args()
    config = resolve_config(args)

    run_dir = build_run_dir(args.results_root, config)
    save_results(run_dir, init_results(config))  # write empty skeleton early

    for dataset_name, length_items in discover_datasets(args.data_root):
        for length_k, path in length_items:
            samples = load_samples(path, config["num_samples"])

            # (a) reference pass: run target model to produce ground truth
            target_model = LocalHFModel(...)
            references = [target_model.generate(s["text"], ...) for s in samples]
            del target_model
            cleanup_cuda_memory()

            # (b) draft pass: run draft/baseline against the reference
            draft_model = SomeDraftModel(...)
            for sample, reference in zip(samples, references):
                result = simulate_sample(draft_model, sample, reference, config)
                ...
            del draft_model
            cleanup_cuda_memory()

            save_results(run_dir, results)  # checkpoint after every length


if __name__ == "__main__":
    main()
```

Conventions:

- Always run target/draft models sequentially and free GPU memory between them with a `cleanup_cuda_memory()` helper (`gc.collect()` + `torch.cuda.empty_cache()`).
- Write results to disk after every length (or every benchmark) so partial runs are recoverable.
- Use `tqdm` bars at distinct `position=` values for nested loops (datasets / lengths / samples / steps).

### Speculative Decoding Eval Flow

For `eval_spec_*.py`:

1. Load dataset preset and discover dataset files by length.
2. Run the target model to produce a ground-truth token sequence per sample.
3. For each sample, simulate speculative decoding with the draft model:
   - `prefill(prompt)` → state
   - Loop: `draft(state, draft_len)` → compare against the reference prefix → `rollback` rejected tokens → `accept(mismatch_token)` to advance.
4. Record per-step traces (draft tokens, accepted tokens, mismatch token, timings, rollback count).
5. Aggregate per-length stats (avg acceptance rate, avg accepted length, decode tps).

Default knobs:

- `NUM_SAMPLES = 2`
- `REFERENCE_LEN = 128`
- `DRAFT_LEN = 5`
- `TEMPERATURES = [0.0]` (extend when sampling-temperature sweeps are needed)

### Accuracy Eval Flow

For `eval_accuracy_*.py`:

1. Define dataset preset, discover tasks via `discover_*_tasks(...)`.
2. Load samples through the benchmark handler.
3. Call `model.generate(...)` per sample; record output, token counts, timings, throughput.
4. Run `postprocess_output(...)` then `score_output(...)` from the benchmark handler.
5. Persist with `BenchmarkResultWriter`.

## Result Layout

One root directory per evaluation entry, e.g.:

- `evaluation/results-spec_main_longin_vX/`

Inside each root, a run directory is created per launch. Two layouts are in use:

- Per-baseline layout:

  `results-<mode>/<run_name>_<timestamp>/<benchmark>/`

  Each `<benchmark>/` contains:

  - `results.json` — full per-sample, per-length, per-dataset details (incl. step traces).
  - `summary.json` — aggregated metrics per length per dataset.
