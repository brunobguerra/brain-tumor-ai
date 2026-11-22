# EDA: Brain Tumor MRI Dataset (Nickparvar)

Source: `notebooks/01-data-understanding.ipynb`. Every number below was printed by that notebook.
Dataset: Kaggle `masoudnickparvar/brain-tumor-mri-dataset`, downloaded 2026-11-21, read from
`data/raw/brain-tumor-mri-dataset/`. `data/raw/` was not modified.

## Inventory

- 7200 files, all `.jpg`. 0 fail to decode.
- Training 1400 and Testing 400 images per class (glioma, meningioma, notumor, pituitary). This matches the publisher's claim with no difference.

## Size and color mode

- 447 distinct (width, height) pairs. 512x512 is the mode (4012 of 5600 in Training, 1002 of 1600 in Testing). Range 150 to 1375 px wide and 167 to 1446 px tall.
- 1533 of 7200 images are not square. Aspect ratio (w/h) ranges from 0.64 to 1.79.
- Color mode, Training: L 2358, RGB 3238, RGBA 3, P 1. Testing: L 709, RGB 891.
- File size median about 21 KB (3.6 to 299 KB).

![Image and file sizes](figures/image-sizes.png)

## Samples

Five random Training images per class (seed 42). Notumor images in this sample are axial slices
with several sequences (T1, T2, FLAIR). The tumor classes in this sample mix axial, coronal and
sagittal planes. Five images per class is a visual check, not a measurement.

![Samples per class](figures/samples-per-class.png)

## Differences by class

| split | class | files | share 512x512 | share mode L | median width | median KB |
|---|---|---|---|---|---|---|
| Training | glioma | 1400 | 1.000 | 0.838 | 512 | 18.5 |
| Training | meningioma | 1400 | 0.905 | 0.396 | 512 | 21.8 |
| Training | notumor | 1400 | 0.012 | 0.016 | 230 | 13.1 |
| Training | pituitary | 1400 | 0.949 | 0.434 | 512 | 28.5 |
| Testing | glioma | 400 | 0.810 | 0.565 | 512 | 20.2 |
| Testing | meningioma | 400 | 0.708 | 0.385 | 512 | 20.2 |
| Testing | notumor | 400 | 0.008 | 0.015 | 227 | 9.6 |
| Testing | pituitary | 400 | 0.980 | 0.808 | 512 | 25.6 |

Pixel intensity, Training only, grayscale at 64x64 (mean of per-image means): glioma 0.130,
meningioma 0.170, notumor 0.242, pituitary 0.194. Share of near-black pixels (below 0.05): 0.510,
0.470, 0.331, 0.321.

![Intensity per class](figures/intensity-per-class.png)

Implication: notumor can be told apart from the other classes by image size, color mode and file
size alone. A high score on this dataset may reflect those cues and not the tumor. The planned
128x128 resize removes the size cue but not the brightness or the acquisition cues. Proposed
controls, not yet agreed: report per-class results, and train a size-and-brightness-only baseline
to see how far metadata alone gets.

## Duplicates (SHA-256 of file bytes)

- Training: 138 hashes appear more than once, 171 redundant files. 5.5% of files belong to a group. Largest group 5 files.
- Testing: 15 hashes, 16 redundant files, 1.9% of files. Largest group 3.
- Redundant files in Training by class: glioma 0, meningioma 14, notumor 119, pituitary 38. In Testing: glioma 14, meningioma 2.
- Testing files whose hash is also in Training: 0. Hashes filed under more than one class: 0.
- Byte hashing misses re-encoded or resized near duplicates, so "no overlap" means no exact overlap. See the next section for the near-duplicate check.

Decision (2026-11-21): keep all files and group by hash when splitting Training into train and
validation. A dry run in the notebook (one seed, in memory) shows a plain stratified 80/20 split
leaves 56 hashes on both sides, while the grouped split leaves 0 and gives exactly 20% validation
with 280 files per class. Testing is not deduplicated, so its 16 redundant files stay in the final score.

## Near duplicates (perceptual hash)

Section 4b of the notebook. Difference hash (dHash, 8x8, 64 bits) with Hamming distance 0, confirmed
by the Pearson correlation of 64x64 grayscale copies at 0.95 or above. Random same-class pairs have a
median correlation of 0.44 and a 95th percentile of 0.68, so 0.95 is a strict cut.

- Testing vs Training: 372 Testing images have a dHash-0 neighbour in Training and 357 are confirmed (22.3% of Testing). Confirmed per class: glioma 6, meningioma 102, notumor 246, pituitary 3. 371 of the 372 share the label of their neighbour.
- Inside Training: 767 images have a dHash-0 neighbour with different bytes and 680 are confirmed (12.1% of Training). Confirmed per class: glioma 2, meningioma 20, notumor 637, pituitary 21.
- These files have different bytes, which is why the exact hash check above found no overlap.

Implications, not yet decided:

- A Testing score is optimistic for notumor and meningioma. Report it with and without the 357 images.
- The hash-grouped split below does not group Training near copies, so some can still sit on both sides of train and validation.

Limit: dHash plus correlation finds re-encoded and resized copies. It does not find crops, flips or other slices of the same patient, so the true overlap can be higher.

## Preprocessing prototype

Section 8 of the notebook. It is exploratory and lives in the notebook until it is accepted and
moved to `brain_tumor_ai/preparation/` with tests.

Split first: every image used here comes from the train side of the hash-grouped split (4480 of the
5600 Training files). Validation and Testing images were not opened. Each step works on one image
with fixed constants, so nothing is estimated from the data.

Pipeline (`preprocess`):

1. Read as grayscale. This also handles the RGBA and P files.
2. Head mask: Gaussian blur 7, Otsu threshold, morphological open and close, largest connected component.
3. Square crop around the mask with a 6% margin, padded with black where it leaves the image. No stretching.
4. Median filter 3.
5. CLAHE, clip limit 2.0, 8x8 tiles.
6. Resize to 128x128 with area interpolation, then divide by 255.

Other techniques shown for comparison: Gaussian and bilateral filters, global histogram
equalization, intensity-level slicing (window 90 to 200), bit-plane slicing, and adaptive thresholding.

![Filters and equalization](figures/preproc-filters-equalization.png)

![Slicing and thresholding](figures/preproc-slicing-threshold.png)

![Pipeline stages](figures/preproc-pipeline.png)

Measured on the 4480 train-side images (5.5 ms per image, 1 mask failure handled by a full-image fallback):

| class | images | median crop side / image side | mean intensity before | mean intensity after |
|---|---|---|---|---|
| glioma | 1120 | 0.943 | 0.130 | 0.220 |
| meningioma | 1120 | 0.966 | 0.169 | 0.261 |
| notumor | 1120 | 0.960 | 0.243 | 0.290 |
| pituitary | 1120 | 1.055 | 0.194 | 0.266 |

What this shows:

- The crop is weak. The Otsu mask covers the whole head, neck and face included, so the box keeps
  94% to 105% of the image side. It mostly trims black borders. Isolating the brain needs skull
  stripping, which is not in the prototype.
- CLAHE lifts the black background and shows noise there (share of zero pixels 0.32 before, 0.00
  after, on one example). Candidates to test: a lower clip limit, or CLAHE inside the mask only.
- The size cue is gone after the 128x128 resize. The brightness cue shrinks but stays: the spread of
  class means goes from 0.113 to 0.070 and notumor is still the brightest. The metadata-only control
  baseline is still advisable.

Grad-CAM readiness: `preprocess` returns `display` (the exact 128x128 image the model sees, the
one to overlay a heatmap on), `model_input` with shape (128, 128, 1) in [0, 1], and `crop_box` in
original pixel coordinates. `heatmap_to_original` places a model-space heatmap back on the original
file. It was checked with a synthetic heatmap only (the hot cell landed half a cell from the crop
centre and the map filled exactly the crop box). There is no model yet.

## Open items

- Decide whether to group Training near duplicates (perceptual hash) in the train and validation split, and how to report Testing with the 357 near-duplicate images removed.
- Decide whether to add a metadata-only control baseline.
- Rule for RGBA and P files is now the OpenCV grayscale read. Confirm it when preparation is written.
- Improve the crop (skull stripping or a tighter box) and test CLAHE inside the mask or with a lower clip.
- Move the pipeline to `brain_tumor_ai/preparation/` with tests, then Grad-CAM after the first model.
