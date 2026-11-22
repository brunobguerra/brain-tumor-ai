# 03 — Data preparation

Status: contract accepted on 2026-11-21. Code is not started.

Preparation runs after data understanding and before any TensorFlow training. The module will be `brain_tumor_ai/preparation/`. Images are handled with OpenCV (`cv2`), dependency `opencv-python-headless`. Tests use synthetic images under `fixtures/`, never real MRI.

## Split

- `assign_splits` accepts only files from the `Training` tree.
- Validation is about 20% of those files, stratified by class, seed 42.
- Files are grouped by the SHA-256 of their bytes. All files sharing a hash go to the same side, so a duplicate never sits in both train and validation. No file is dropped. Because groups hold up to 5 files, the 20% share and the class balance are approximate, and the code reports the realized counts.
- Train and validation indices are disjoint and are written under `data/interim/` before any fitted statistic.
- `Testing` has its own loader. That loader cannot be passed to `fit`.

`data/raw/` stays immutable. Fitted parameters go to `data/interim/`. Materialized arrays, if we write them, go to `data/processed/`. None of those directories are committed.

## OpenCV baseline

Configured later in `configs/baseline.yaml`:

- paths to the raw `Training` and `Testing` trees
- seed `42`
- validation fraction `0.2`
- grayscale
- resize to 128×128
- scale pixels by dividing by 255, into `[0, 1]`

Resize and division by 255 are deterministic. They are not estimated from data. Any learned statistic (mean, standard deviation, CLAHE or equalization parameters) is fit on the training indices only, saved under `data/interim/`, and applied unchanged to validation and test. Augmentation, when a later experiment adds it, runs on the training split only.

## Tests the module must pass

- Train and validation indices are disjoint and stratified.
- No SHA-256 appears in both train and validation.
- Calling `fit` on the test split raises.
- Changing the test batch does not change parameters already fit on train.
- A path that appears in both train and test is refused.
- `Testing` is not regrouped or deduplicated. Its 16 redundant files stay in the final score, and the report says so.

## Credentials

If a Kaggle download needs a token, the variable names go in `.env.example` and the values stay in `.env`. Real scans are not pasted into chat, issues, commit messages, or fixtures.
