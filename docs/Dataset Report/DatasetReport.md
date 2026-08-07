# Crowd Gender Dataset

A manually curated computer vision dataset built from real CCTV footage, labeled for **gender-based person detection**, demographic analysis, and crowd analytics.

---

## Overview

This dataset was created by extracting frames from CCTV footage collected at two real-world locations — an **agri-manure shop** and a **medical lab** — over several hours of recording. Frames were extracted, filtered, and manually annotated with bounding boxes across two classes: **Male** and **Female**.

It is intended to support:
- Gender-based person detection
- Demographic analysis
- Crowd analytics in surveillance-style footage

---

## Dataset Statistics

| Attribute | Value (approx.) |
|---|---|
| Total images | 489 |
| Total annotations | 1,463 (3.0 per image, average) |
| Number of classes | 2 (Male / Female) |
| Image resolution | 480p – 720p |
| Average image size | 0.41 MP (range: 0.01 – 2.07 MP) |
| Median image ratio | 848 × 478 (wide) |
| Annotation tool | Roboflow |
| Annotation type | Bounding box |
| Missing annotations | 0 |
| Null examples | 0 |

### Annotations by class

| Split | Male (incl. duplicates) | Female (incl. duplicates) | Unique Male | Unique Female |
|---|---|---|---|---|
| Train | 1,038 | 247 | 35 | 5 |
| Validation | 108 | 6 | 19 | 2 |
| Test | 57 | 4 | 12 | 2 |

> **Note:** The dataset is currently heavily imbalanced toward male samples. See [Known Limitations](#known-limitations--future-work) below.

### Dataset split (89% : 7% : 4%)

| Split | Images |
|---|---|
| Train | 435 |
| Validation | 35 |
| Test | 20 |

---

## Data Collection Pipeline

```
CCTV Footage
    → Video format conversion (H.264)
    → Frame extraction (every 10–20 sec)
    → Quality filtering (removed duplicates & blurry frames)
    → Upload to Roboflow
    → Batch-wise manual annotation
```

### Capture conditions
- **Locations / camera angles:** 2 fixed angles, covering both the agri-manure shop and the medical lab
- **Human poses:** standing, walking, sitting, leaning, turning
- **Lighting conditions:** natural light, artificial light, bright, moderate, low illumination

---

## Related / Reference Dataset: PETA

Age and additional gender modeling in this project also draws on the **PETA (PEdesTrian Attribute) dataset**, a public benchmark built from 10 smaller pedestrian re-identification datasets, purpose-built for attribute recognition on real, low-resolution surveillance footage.

| Attribute | PETA |
|---|---|
| Images | ~19,000 |
| Unique pedestrians | 8,705 |
| Attributes | 65 total (61 binary + 4 multi-class); 35 commonly used |
| Standard split | 9,500 train / 1,900 val / 7,600 test |

**How PETA is used in this project:**
- `personalMale` — used for gender pretraining (~19,000 labeled samples)
- `personalLess30` / `personalLess45` / `personalLess60` / `personalLarger60` — remapped to four age buckets: `Age16-30`, `Age31-45`, `Age46-60`, `AgeAbove61`
- `personalLess15` was **dropped** — it doesn't cleanly map to any bucket and was found to cause underfitting due to too few samples

Local user data (this dataset) is used for **Stage 2 gender fine-tuning**, adapting a PETA-pretrained model to the deployment domain (local CCTV / entrance footage).

---

## Curation Decisions

- Explicit train/val/test split management to keep age-model results comparable to the PETA baseline
- Class imbalance identified (age groups 31–45 and 16–30 dominate sample counts) — directly informed the `class_weight='balanced'` setting during hyperparameter tuning
- Duplicate and blurry frames removed during quality filtering to reduce label noise

---

## Known Limitations & Future Work

- **Gender imbalance:** the dataset is currently skewed heavily toward male samples (both in this local dataset and in PETA), which limits model performance on female detection/classification.
- **Ongoing mitigation:** additional **publicly available CCTV footage is actively being sourced** to increase female sample counts and reduce this imbalance in future dataset versions.
- **Scale:** with only 489 images from 2 locations, generalization to unseen camera angles, lighting, or demographics should be validated before production use.

---

## Repository Structure

```
.
├── data/
│   ├── user/
│   │   ├── male/       # Hand-labeled crops used for Stage 2 gender fine-tuning
│   │   └── female/     # Hand-labeled crops used for Stage 2 gender fine-tuning
├── train/
├── valid/
├── test/
└── README.md
```

---

## Usage

This dataset is exportable from Roboflow in most common formats (YOLO, COCO, Pascal VOC, etc.). Update this section with the exact export format and loading instructions once finalized, e.g.:

```python
# Example: loading a YOLO-format export
from pathlib import Path

data_yaml = Path("data.yaml")
# ... your training / inference code here
```

---

## Citation

If you use this dataset, please cite it as:

```
@misc{crowd_gender_dataset,
  title        = {Crowd Gender Dataset},
  author       = {<Your Name / Team>},
  year         = {2026},
  note         = {Manually annotated CCTV-derived dataset for gender-based person detection}
}
```

If you also use PETA-derived labels/pretraining, please cite the original PETA dataset paper as well.

---

## License

Specify your dataset license here (e.g., CC BY-NC 4.0, MIT, or a custom research-only license). CCTV footage of real people may carry privacy considerations — confirm consent/anonymization policies before public release.

---

## Contributors

- Dataset curation & annotation: *add names*
- Report: *add names*
