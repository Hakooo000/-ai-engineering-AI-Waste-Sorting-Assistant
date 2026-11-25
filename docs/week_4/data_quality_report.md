# Data Quality & Bias Audit Report — AI Waste Sorting Assistant

## 1. Project and Data Overview
* **Project Purpose:** The AI Waste Sorting Assistant classifies household waste images into 5 recyclable categories (`Cardboard`, `Glass`, `Metal`, `Paper`, `Plastic`) and displays verified sorting guidance to reduce domestic recycling contamination.
* **Linked Topic 2 Requirements:**
  * **FR2 (Functional):** Classify uploaded items into supported standard waste categories without inventing undefined labels.
  * **NFR2 / NFR3 (Non-Functional):** Achieve $\ge 85\%$ Top-1 Accuracy and $\ge 0.80$ Macro F1-score across all 5 waste classes.
  * **RAI1 (Responsible AI):** Flag low-confidence or ambiguous inputs rather than presenting uncertain guesses as facts.
* **Topic 3 Pathway & Audited Artefacts:**
  * **Pathway:** Pathway C (Computer Vision / Images).
  * **Audited Output (Part B):** Preprocessed `224x224` RGB `.png` images stored persistently in Google Drive under `/content/drive/MyDrive/week3_project/data/processed/images/` (organized into 5 class subfolders) and `/content/drive/MyDrive/week3_project/data/processed/image_manifest.csv`.
  * **Matching Lineage Manifest:** `/content/drive/MyDrive/week3_project/data/week3_manifest.json`.
* **Source & Access Status:** Mendeley Data Waste Classification Dataset (`https://data.mendeley.com/datasets/h5pxbsdz4m/1`, Version 1), open-access dataset permitted for academic and research use.
* **Audit Population, Sample & Limitations:**
  * **Inspection Unit:** Individual preprocessed `.png` image and its corresponding manifest row.
  * **Population & Sample:** Full manifest metadata check (`N = 739`) combined with a stratified visual inspection sample of **15 images** (3 representative images per class across `Cardboard`, `Glass`, `Metal`, `Paper`, and `Plastic`).
  * **Limitation:** Visual inspection of 15 images confirms pipeline formatting and class label alignment but cannot detect every mislabeled edge case across all 739 images without exhaustive manual review.

---

## 2. Five-Dimension Quality Scorecard

| Dimension | Finding | Sample / Evidence | Impact on Intended Task | Decision |
| :--- | :--- | :--- | :--- | :--- |
| **Completeness** | **Observed:** All manifest rows contain complete source paths, output paths, SHA-256 hashes, and dimensions; all 5 class folders are populated. | `df.isna().sum()` on `image_manifest.csv` returned `0` missing fields across all `739` records; all 5 folders (`Cardboard`, `Glass`, `Metal`, `Paper`, `Plastic`) contain valid `.png` files. | Ensures uninterrupted batch loading (`ImageFolder` / CSV loader) with no dropped labels during model training (**FR2**). | **Pass** |
| **Consistency** | **Observed:** All inspected images share identical `224x224` resolution, `RGB` 3-channel color mode, and `.png` format. | 15/15 sampled images (e.g., `UHW_Cardboard (1)_72e93532257e.png`, `UHW_Glass (1)_ccef76b1e159.png`) verified via PIL returned `size=(224, 224)` and `mode='RGB'`. | Eliminates tensor shape or channel mismatch errors during neural network ingestion. | **Pass** |
| **Accuracy** | **Observed:** Spot-checked images visually match their assigned class subfolders (`Cardboard`, `Glass`, `Metal`, `Paper`, `Plastic`). | Manual visual check of 15 sampled images (3 per class) confirmed 15/15 items depict the physical waste material matching their folder label. | Directly supports the $\ge 85\%$ classification accuracy target (**NFR2**); prevents teaching the model wrong material associations. | **Pass** |
| **Timeliness** | **Inferred Risk / Not Verified:** Dataset reflects common packaging designs from its Mendeley v1 publication, though newer regional biodegradable/composite packaging formats are unverified. | Mendeley Data Waste Classification Dataset v1 (`h5pxbsdz4m/1`); no live continuous ingestion stream is used in V1. | Acceptable for standard rigid recyclables, but composite or region-specific Malaysian packaging (e.g., laminated food wrappers) may see lower real-world accuracy. | **Warning** |
| **Uniqueness** | **Observed:** Output filenames are unique via SHA-256 hash suffixes, but `11` exact duplicate source hashes exist in the dataset. | `df["source_sha256"].duplicated().sum()` on `image_manifest.csv` identified `11` duplicate source files out of `739` total rows. | Uncontrolled duplicate images across train/test splits can cause data leakage and artificially inflate validation accuracy (**NFR2**). | **Warning** |

---

## 3. Validation Findings

| Rule | Risk / Related Req | Condition & Evidence Source | Items Tested | Observed Result | Action & Justification |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **V1: Image Integrity & Tensor Shape** | Prevents pipeline crashes and invalid model inputs (**FR1 / NFR2**). | Every output `.png` file referenced in `image_manifest.csv` must exist on disk, be non-empty (`> 0` bytes), have mode `RGB`, and dimensions `(224, 224)`. | `739` manifest rows + `15` PIL image checks | `739/739` files exist & non-empty (`57,060`–`125,268` bytes in sample); **15/15** PIL checks passed (`RGB`, `224x224`). | **Pass (Keep):** All prepared files satisfy tensor input requirements. |
| **V2: Label-to-Folder Alignment & Duplicate Gate** | Prevents class mislabeling and train/test leakage (**NFR2 / NFR3**). | Every output path in `image_manifest.csv` must map to one of the 5 valid class subfolders (`Cardboard`, `Glass`, `Metal`, `Paper`, `Plastic`) and contain zero duplicate `source_sha256` hashes. | `739` rows in `image_manifest.csv` | **739/739 (100%)** mapped to valid class folders; **11** duplicate `source_sha256` hashes detected (`728` unique source images). | **Warning (Quarantine Duplicates):** Deduplicate the 11 identical `source_sha256` entries before splitting into train/validation/test sets in Topic 5. |

---

## 4. Duplicate, Anomaly and Coverage Findings
* **Duplicates & Anomalies:** Output filenames (`{stem}_{hash[:12]}.png`) prevent file-system collisions, while SHA-256 hash auditing revealed **11 exact duplicate raw images** (`728` unique images out of `739`). No corrupted or 0-byte images were found in `data/processed/images/`.
* **Category Coverage Analysis:**

| In-Scope Category / Case | Expected Evidence | Observed Evidence / Gap | Possible Downstream Effect |
| :--- | :--- | :--- | :--- |
| **Minority Classes (`Glass` & `Metal`) vs. Majority Classes (`Cardboard` & `Plastic`)** | Balanced image counts across all 5 categories to prevent majority-class bias. | **Observed:** Severe class imbalance across the 739 images: `Cardboard` = **306** (41.4%), `Plastic` = **210** (28.4%), `Paper` = **94** (12.7%), `Metal` = **80** (10.8%), and `Glass` = **49** (6.6%). | Model may bias predictions toward `Cardboard` or `Plastic`, lowering recall on `Glass` (only 49 samples) and threatening the $\ge 0.80$ Macro F1 target (**NFR3**). |
| **Degraded / Occluded / Ambiguous Items** (Cross-class edge case) | Examples of dirty, crushed, or multi-material waste items to test confidence thresholds. | **Inferred Risk / Not Verified:** Dataset primarily contains clean, single-object tabletop/studio photos (`UHW_*`); heavily soiled or ambiguous items are underrepresented. | Model may output overly confident predictions on messy real-world household trash, weakening **RAI1** low-confidence flagging. |

---

## 5. Bias Assessment
* **Identified Bias Sources (Representation & Pipeline-Induced Bias):**
  1. **Representation Bias (Class Imbalance & Studio Capture):** `Cardboard` (306) has more than $6\times$ the samples of `Glass` (49), and the Mendeley dataset predominantly features isolated, well-lit waste items rather than cluttered domestic bins or region-specific Malaysian packaging.
  2. **Pipeline-Induced Bias (Fixed `224x224` Aspect-Ratio Resizing):** Resizing rectangular images directly to square `224x224` tensors compresses elongated objects (such as tall plastic bottles or flattened cardboard), slightly distorting geometric proportions.
* **Causal Reasoning Chain:**
  * *Source / Preparation Decision:* Training on an imbalanced dataset (49 `Glass` vs. 306 `Cardboard`) consisting of clean single-object photos resized without aspect-ratio padding $\rightarrow$ *Data Skew:* Underrepresentation of `Glass`/`Metal` items, cluttered backgrounds, and varied lighting $\rightarrow$ *Potential Effect:* Lower recall on minority classes (`Glass`, `Metal`) and overconfident false positives when users upload dim or cluttered waste photos $\rightarrow$ *Proposed Control:* Use class-weighted loss or targeted augmentation (flips, rotations, brightness/contrast jitter) for minority classes during training, and enforce a strict confidence threshold ($\tau \ge 0.70$) with a low-confidence user warning (**RAI1**).

---

## 6. Privacy, Rights and Governance
* **Provenance & Versioning:** Sourced from Mendeley Data Waste Classification Dataset (`https://data.mendeley.com/datasets/h5pxbsdz4m/1`, v1). Processed outputs are tracked via SHA-256 checksums in `data/processed/image_manifest.csv` and `data/week3_manifest.json`.
* **Rights & Permissions:** Publicly shared on Mendeley Data for academic and research use. Raw and processed datasets are stored in a private Google Drive workspace (`/content/drive/MyDrive/week3_project/`), while only non-sensitive metadata, manifests, and code are pushed to public GitHub.
* **Privacy & Sensitive Data:** **Observed:** Audited sample (`15` images) and file prefixes (`UHW_*`) contain exclusively inanimate waste objects (`Cardboard`, `Glass`, `Metal`, `Paper`, `Plastic`); no human faces, license plates, or personal identifiable information (PII) are present.
* **Consent / Collection Basis:** Not applicable to human subjects as the dataset contains only physical waste objects published under open academic research terms.
* **Ongoing Maintenance & Safeguard:**
  * *Safeguard:* Keep raw/processed image binaries in private Google Drive storage (`data/raw/` and `data/processed/images/` excluded via `.gitignore`) and commit only `image_manifest.csv`, `week3_manifest.json`, and documentation to GitHub.
  * *Maintenance Owner:* Project author (Ahmad Haziq) will review class balance and trigger revalidation whenever new local Malaysian waste images or additional waste categories are added.

---

## 7. Mitigation and Impact on Requirements

| Priority Improvement | Action Owner & Timing | Affected Topic 2 Requirements | Residual Risk Remaining |
| :--- | :--- | :--- | :--- |
| **1. Deduplicate 11 `source_sha256` Duplicates before Splitting:** Remove the 11 exact duplicate images (leaving 728 unique images) prior to partitioning so identical items never leak across train and test sets. | Project Author — Before Topic 5 baseline training | **NFR2** (Top-1 Accuracy $\ge 85\%$), **NFR3** (Macro F1 $\ge 0.80$) | Near-duplicate photos (same object photographed from a slightly shifted angle) may still exist without perceptual hashing. |
| **2. Mitigate Class Imbalance (`Glass`: 49) & Enforce Confidence Gate ($\tau = 0.70$):** Apply class-weighted loss / data augmentation for minority classes (`Glass`, `Metal`, `Paper`) and reject low-confidence predictions in the UI. | Project Author — During Topic 5/6 model training & UI integration | **NFR3** (Macro F1 $\ge 0.80$), **RAI1** (Flagging ambiguous inputs $\ge 95\%$), **FR4** (Low-confidence warning) | Out-of-distribution items (e.g., hazardous batteries or e-waste) may still occasionally trigger high confidence until explicit out-of-scope testing is run. |

---

## 8. Quality-Gate Decision
* **Decision:** **GO WITH CONDITIONS** (for the 739-image, 5-class Mendeley Waste Classification dataset stored in `/content/drive/MyDrive/week3_project/data/processed/images/`).
* **Justification Evidence:**
  1. **Structural & Format Readiness:** 100% (`739/739`) of audited images in `image_manifest.csv` exist, are non-empty, have `0` missing metadata fields, and conform to `224x224` 3-channel `RGB` format inside their respective class subfolders.
  2. **Zero Privacy or Licensing Blockers:** The dataset is open-access on Mendeley Data and contains zero PII or sensitive human imagery.
* **Mandatory Conditions Before Downstream Evaluation:**
  1. Remove the **11 exact duplicate `source_sha256` entries** (`728` unique images remaining) prior to creating train/validation/test splits so evaluation metrics are free from data leakage.
  2. Address the severe class imbalance (`Glass` = 49 vs. `Cardboard` = 306) via class-weighted loss or targeted augmentation, and implement the confidence-threshold warning gate (**RAI1**) during inference.
* **Trigger for Re-Review:** Re-audit will be triggered once deduplication is executed or if baseline validation Macro F1 on `Glass` and `Metal` falls below `0.80` (**NFR3**).
