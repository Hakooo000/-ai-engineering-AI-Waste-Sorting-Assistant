# Week 3 Pipeline Decisions & Stage Contracts — AI Waste Sorting Assistant

## 1. Traceability to Topic 2 Requirements
* **FR1 & FR2 (Image Ingestion & Classification):** Necessitates automated validation of raw image file types and resolution standardization.
* **NFR1 (p90 Latency < 3,000 ms):** Governs resizing to $224 \times 224$ pixels to minimize GPU memory pressure and inference latency.
* **NFR2 & NFR3 (Top-1 Accuracy $\ge 85\%$ & Macro F1 $\ge 0.80$):** Demands consistent 3-channel RGB color space normalization to prevent tensor shape mismatch during inference.
* **RAI1 (Low-Confidence & Ambiguity Handling):** Enforces strict quarantine and flagging stages when image headers are corrupted or unreadable.

---

## 2. Stage Contracts & Failure Handling

| Pipeline Stage | Stage Contract & Preconditions | Observable Evidence | Defensive Failure Response |
| :--- | :--- | :--- | :--- |
| **1. Ingest** | Source directory `data/raw/...` exists and contains supported image file extensions (`.png`, `.jpg`, `.jpeg`, `.webp`). | Input count > 0 confirmed by `rglob("*")` recursive listing in Cell 3. | Raise `FileNotFoundError` or halt execution with `assert files` if no valid input files are found. |
| **2. Inspect** | File streams must be parseable by image loader; file headers must not be corrupted. | PIL `.verify()` completes without exception; records extracted for image dimensions and modes. | Catch `UnidentifiedImageError` and `OSError`; append failed items to `failed_files` quarantine log rather than crashing pipeline. |
| **3. Prepare / Transform** | Image converted to 3-channel RGB; resized to $224 \times 224$ using high-quality resampling. | Output tensor properties conform to $224 \times 224 \times 3$; deterministic hash filename generated (`{stem}_{hash[:12]}.png`). | Raise descriptive `ValueError` if resizing produces null dimensions or unsupported color depths. |
| **4. Validate Output** | Prepared image files written to target directory; file size > 0 bytes; output count equals valid input count. | File size assertion and check that `stat().st_size > 0`; exported manifest row count equals input count. | Raise `AssertionError("Prepared output missing or empty")` and halt pipeline progression. |
| **5. Record Lineage** | Explicit 1-to-1 mapping recorded between raw source SHA-256 and prepared output SHA-256. | Written to `data/week3_manifest.json` and verified in Colab Cell 6B / Cell 9. | Halt pipeline if manifest record count mismatches registered transformations or checksums deviate. |

---

## 3. Engineering Decisions & Accepted Trade-offs

### Decision 1: ETL (Extract, Transform, Load) vs. ELT Pattern
* **Selected Approach:** Pure ETL pattern.
* **Rationale:** In computer vision, passing raw, arbitrarily sized images to downstream models causes out-of-memory errors and variable batching latency. Normalizing color spaces and dimensions *before* persistent storage guarantees deterministic model inputs.
* **Trade-off Accepted:** Storing pre-resized $224 \times 224$ images permanently discards high-frequency details from the original $2048 \times 1536$ camera captures, but provides reliable inference speed and reduced disk usage.

### Decision 2: Batch Processing vs. Live Streaming
* **Selected Approach:** Deterministic micro-batch offline execution.
* **Rationale:** The application scope (FR1) addresses single-item photo captures uploaded by users at waste bins. A lightweight batch pipeline satisfies requirements without introducing Kafka or streaming cluster overhead.
* **Trade-off Accepted:** Continuous real-time video stream ingestion is not supported in V1.

### Decision 3: Aspect Ratio Handling (Direct Resize vs. Letterbox Padding)
* **Selected Approach:** Direct resize to $224 \times 224$.
* **Rationale:** Direct resizing enables uniform tensor stacking and simplified preprocessing code for the semester prototype.
* **Trade-off Accepted:** Slight geometric distortion on long, thin waste items (e.g., fluorescent tubes, tall bottles), which will be monitored during Topic 4 model evaluation.
