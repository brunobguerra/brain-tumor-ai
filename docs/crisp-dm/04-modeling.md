# 04 — Modeling

Status: not started. Starts only after `docs/crisp-dm/03-data-preparation.md` is implemented and its tests pass.

## Model

A small convolutional network in TensorFlow/Keras, trained from scratch on CPU. `tensorflow` is added with `uv add` at the start of this phase, not before.

The first training run lives in `notebooks/02-baseline-cnn.ipynb`. The notebook imports the persisted split and the OpenCV pipeline. It does not rebuild the split or refit preprocessing.

- Train on the training indices.
- Choose checkpoints and any training hyperparameters on the validation indices.
- Touch the official test set once, in evaluation, not in this loop.
- Seed 42 for every random operation the library exposes.

After the notebook runs top to bottom, the network moves into `brain_tumor_ai/modeling/` with a failing test first, then an entrypoint that reads `configs/baseline.yaml`.

## Out of this phase

A second architecture, transfer learning, and GPU training wait until this baseline exists and can be rerun.
