# 05 — Evaluation

Status: not started. Starts only after a baseline model exists.

## Rule

The official `Testing` folder is scored once. Model selection uses validation. A metric that was used to pick the model is not recomputed on the test set in a loop.

Report macro-F1 (primary), accuracy, and the confusion matrix, with the dataset version or the date the files were placed in `data/raw/`.

The code will live in `brain_tumor_ai/evaluation/` and an `entrypoints` script that loads the saved model, the fitted preparation parameters, and the test loader. After that path exists, run the leakage reviewer on it.

Do not publish a number from this document. There is no measured result yet.
