# brain-tumor-ai

Project specific context. General conventions live in the workspace `CLAUDE.md` two folders up (`projects/<name>/` sits inside the workspace).

## Goal

Classify one MRI from the Nickparvar dataset into glioma, meningioma, pituitary, or no tumor. Cycle 1 is a reproducible study, not a diagnostic tool. The protocol is `docs/crisp-dm/01-business-understanding.md`.

## Data

- Cycle 1: [Brain Tumor MRI Dataset](https://www.kaggle.com/datasets/masoudnickparvar/brain-tumor-mri-dataset) (Masoud Nickparvar). Four classes, label = folder name (`glioma`, `meningioma`, `pituitary`, `notumor`). Place the official `Training/` and `Testing/` trees under `data/raw/brain-tumor-mri-dataset/` and do not modify them. Data is not committed.
- Later, as its own study: Cheng et al. on Figshare, DOI `10.6084/m9.figshare.1512427`. Not an external test of the Nickparvar model until image overlap is measured.

## Commands

- Install: `uv sync`
- Test: `uv run pytest`
- Lint: `uv run ruff check . && uv run ruff format --check .`

## Notes

Add decisions, known data issues and open questions here as the project evolves.
