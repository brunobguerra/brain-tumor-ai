# 02 — Data understanding

Status: not started. This file lists what the first notebook must answer. It does not contain measured counts.

## Dataset

Brain Tumor MRI Dataset, Masoud Nickparvar:

https://www.kaggle.com/datasets/masoudnickparvar/brain-tumor-mri-dataset

Place the official `Training/` and `Testing/` trees under `data/raw/brain-tumor-mri-dataset/` and leave those files unchanged. `data/` is not committed. The label is the class folder name: `glioma`, `meningioma`, `pituitary`, `notumor`.

The publisher describes 7,200 images, four balanced classes, 1,400 training and 400 testing images each. Those figures are the publisher's claim. The notebook has to count the files that are actually on disk.

## What the notebook must report

Write `notebooks/01-data-understanding.ipynb` and `reports/eda.md` with:

- File counts per class in `Training` and in `Testing`, and how they compare with the publisher's claim.
- Image height, width, and color mode (grayscale vs color). Sizes are known to vary.
- Files that fail to decode.
- Exact duplicate files by content hash inside each split, and hashes that appear in both `Training` and `Testing`.

No model in this phase. No preprocessing that is later reused for training. Reading images to describe them is allowed; fitting a scaler, a normalization statistic, or an augmentation policy is not.

## Later dataset

Cheng et al., Figshare brain tumor dataset, DOI `10.6084/m9.figshare.1512427`. Three classes, patient ids, tumor borders. It is a separate study. It is not loaded in this phase.
