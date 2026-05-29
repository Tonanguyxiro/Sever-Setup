# General

This `AGENTS.md` defines shared conventions for all research projects. For module-specific rules, see the `AGENTS.md` inside each module (e.g. `src/AGENTS.md`, `evaluation/AGENTS.md`, `test/AGENTS.md`, `motivation/AGENTS.md`).

## Environment

- Use `${PROJECT_ROOT}/.venv/bin/python` as the Python interpreter.
- The environment is managed by `uv` and defined in `pyproject.toml`.

## Import And Run Rules

- Run repository-owned Python scripts from the repo root.
- Scripts must be runnable via `uv run path/to/file.py` from the repo root. `uv run -m package.module` is also fine when a module entry point is clearer.
- Do not inject `sys.path` in repository-owned code under `src/`, `test/`, `evaluation/`, or `motivation/`.
- Manage imports and dependencies through `pyproject.toml` and `uv`. Do not import modules from nested sub-subdirectories of another package.
- If a script requires a GPU, do not run it yourself — leave it to the user.

## Modules Overview

The repo is organized into one library module and three research modules:

- `src/` — core library shared by all other modules.
- `evaluation/` — formal experiments and benchmarks.
- `motivation/` — small exploratory experiments to validate ideas or surface pain points.
- `test/` — correctness tests for `src/`.

### `src/` — Core Library

`src/` is the shared library used by training, tests, evaluation, and motivation scripts. Code here should be:

- Clean and well-structured.
- Modular and easy to extend.

### `evaluation/` — Experiments

Runs experiments and evaluates implemented algorithms. Layout:

- `baseline/` — comparison baselines, each wrapped as a class with a unified interface.
- `benchmark/` — benchmarks and datasets, with runners and wrappers for execution and result handling.
- `eval_XXX.py` — evaluation entry points that orchestrate baselines and benchmarks.
- `results-XXX/` — output directory; the `XXX` suffix matches the corresponding `eval_XXX.py`.

Also, `eval_XXX.py` and `results-XXX/` might be grouped (e.g. `eval_<topic>/`) if there are multiple evaluation scripts for the same topic.

#### `baseline/` Rules

Files:

- `__init__.py` — shared implementation and public exports.
- `baseline_XXX.py` — baseline-specific logic.

Responsibilities:

- A baseline handles only: model invocation, prompt preprocessing, timing, and unified output.
- A baseline does **not** handle benchmark data loading, metric computation, or result aggregation.

#### `benchmark/` Rules

Files:

- `__init__.py` — shared implementation and public exports.
- `benchmark_XXX.py` — benchmark-specific logic.

Responsibilities:

- A benchmark handles: sample construction, post-processing, scoring, and writing results.
- A benchmark does **not** drive the main evaluation loop.

#### `eval_XXX.py` Rules

Each `eval_XXX.py` implements one evaluation pipeline by orchestrating baselines and benchmarks.

- Naming: `eval_<topic>_<method>_<extra>.py`, where `<extra>` typically encodes a version or variant tag (e.g. `v2`).

Recommended script structure:

```python
# Imports

## Stdlib
import os
import sys
import time
import logging
import argparse

## Third-party
import numpy as np
import torch

## Project
from src import xxx

# Default parameters and configurations
DATAROOT = "path/to/dataset"
RESULT_DIR = "path/to/save/results"
RUN_NAME = "experiment_name"

""" Keep frequently tweaked configs (model paths, etc.) here for easy editing. """


def parse_args() -> argparse.Namespace:
    parser = argparse.ArgumentParser()
    parser.add_argument("--data-root", type=str, default=DATAROOT)
    parser.add_argument("--result-dir", type=str, default=RESULT_DIR)
    parser.add_argument("--run-name", type=str, default=RUN_NAME)
    return parser.parse_args()


def resolve(args: argparse.Namespace):
    # Resolve cmd arguments into runtime config.
    pass


def main():
    args = parse_args()

    time_str = time.strftime("%m%d-%H%M", time.localtime())
    output_dir = os.path.join(args.result_dir, f"{args.run_name}_{time_str}")

    # ... run experiment ...

    # ... save results ...


if __name__ == "__main__":
    main()
```

### `motivation/` — Exploratory Experiments

Used for small experiments that probe existing code, validate ideas, or surface pain points.

Each test should be put under a folder (e.g. motivation/moti_decode_latency/) wich contains:

- `moti_decode_latency.py` — the main script that runs the experiment.
- `moti_decode_latency_vis.ipynb` — (optional) a notebook that visualizes the results.
- `results/` — (optional) a folder to save results.

### `test/` — Correctness Tests

Tests the code in `src/` and verifies correctness.

- Verify via logs: `src/` outputs are complex, so log all important information and assert on log content rather than return values alone.

The test modules should be put in a folder (e.g. `test/test_src_gist_cache/`) which means we test the gist cache in `src/` mudules, each folder contains:

- `test_src_gist_cache.py` — the main test script.
- `test_src_gist_cache_vis.ipynb` — (optional) a notebook that visualizes the results.
- `log/` — (optional) a folder to save outputs.
