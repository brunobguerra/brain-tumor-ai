# 01 — Business understanding

Status: accepted on 2026-11-21.

## Question

Given one MRI from the Nickparvar Brain Tumor MRI dataset, which of four classes is it: glioma, meningioma, pituitary, or no tumor?

Cycle 1 answers that question with a reproducible study. It does not diagnose a patient.

## Success

The held-out official `Testing` folder is scored once, after model selection.

- Primary metric: macro-F1.
- Also reported: accuracy and the confusion matrix.
- There is no target accuracy. A finished cycle is a baseline that another person can rerun from this repo, with the split and the seed fixed.

## Decisions for this cycle

- Framework for the model: TensorFlow/Keras, a small CNN trained from scratch on CPU. The `tensorflow` dependency is added only when modeling starts.
- Vision: OpenCV (`import cv2`) via `opencv-python-headless`.
- Baseline image contract: grayscale, resize to 128×128, scale pixels to `[0, 1]`. No ImageNet normalization and no augmentation on the baseline.
- Split: `Testing` is untouched until final evaluation. Validation is 20% of `Training`, stratified by class, seed 42. Indices are written under `data/interim/` before any fitted statistic.
- Process map: the six files in this folder. Code lands in `brain_tumor_ai/preparation/`, then `modeling/`, then `evaluation/`, each when that phase has a testable behavior.

## Non-goals for cycle 1

- Clinical use, a served model, Docker, or a dashboard.
- Treating the Cheng/Figshare dataset as an external test of a model trained on Nickparvar. The Kaggle author states that Figshare glioma images replaced the SARTAJ glioma images, so overlap has to be measured first.
- The Notion Colab guide (frozen ResNet-18, five epochs, PyTorch). It is a tutorial, not this protocol.
- ZenML, MLflow, DVC, or any other orchestrator. A single YAML file is the configuration for later phases.

## What comes after this cycle

In order: data understanding, data preparation, the TensorFlow baseline, extraction into the package, then OpenCV improvements one at a time, a second model only if a GPU is available, a separate Cheng study with a patient-level split, deployment notes, and an English paper. The paper follows the Overleaf project once its section list is exported. That project is private and its outline is not in the repo yet.

## Open checks

- Counts, image sizes, color modes, and hash overlap between `Training` and `Testing` are unverified until the data-understanding notebook runs.
- The hatch package path in `pyproject.toml` points at `src/brain_tumor_ai`, which does not exist. The code lives in `brain_tumor_ai/`. Fix that in the same edit that first changes `pyproject.toml`.
