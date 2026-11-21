# brain-tumor-ai

One paragraph: the question, the data, the main result.

## Setup

```bash
uv sync
```

## Data

Describe where each dataset comes from and how to place it in `data/raw/`. Data is not committed.

## Structure

```
brain_tumor_ai/  Python package (all reusable .py code)
entrypoints/     runnable scripts (train, evaluate, serve)
deploy/          deployment files (Docker, CI/CD, serving config)
docs/            documentation and design notes
fixtures/        small test data used by tests (never real patient data)
tests/           tests
scratch/         throwaway experiments, not tracked
notebooks/       numbered exploration notebooks
data/raw         original data, immutable, not tracked
data/interim     intermediate data, not tracked
data/processed   final modelling data, not tracked
```

## Results

Summarise findings here with the dataset version used.
