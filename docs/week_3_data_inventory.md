# Week 3 Data Inventory — AI Waste Sorting Assistant

| Source ID | Filename / URL | Type and Purpose | Version / Date / Owner | Access / Rights | Inspection Evidence |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **S01** | `data/raw/topic3_dummy_datasets/03_images/` (or TrashNet Repository: `https://github.com/garythung/trashnet`) | Unstructured RGB/PNG/JPG image dataset used for training, testing, and evaluating the waste category classification model. | v1.0 (Gary Thung & Mengyang Yang, Stanford University) | Open-source research benchmark (Public / MIT / Academic research use) | Inspected via PIL in Colab Cell 3/5C: verified readable image headers across sample files (`shape_01.png` - `shape_06.png`); native dimensions range from $512 \times 384$ to $2048 \times 1536$; color modes include 3-channel RGB; zero corrupted file streams detected. |
| **S02** | `data/raw/swcorp_guidelines.pdf` (SWCorp Malaysia / U.S. EPA Municipal Recycling Reference Manuals) | Semi-structured reference guidelines providing standard domestic category disposal instructions and safety disclaimers. | 2024–2026 Official Municipal Manuals | Public government publication for citizen recycling guidance | Inspected via text parser: confirmed presence of distinct operational instructions for 6 domestic waste categories (plastics, paper, metal, glass, organics, and hazardous/e-waste). |

---

### Ingestion Observations & Quality Caveats
1. **Resolution Variability:** Raw consumer camera uploads vary widely in native aspect ratio and resolution. Fixed tensor normalization ($224 \times 224 \times 3$) is required prior to model inference.
2. **Ambiguous Lighting:** Photos captured under poor indoor lighting or cluttered backgrounds may require confidence score filtering during inference (RAI1 thresholding at $\tau = 0.70$).
3. **Lineage Linkage:** All sample images ingested in Part B have been hashed via SHA-256 and mapped directly to prepared output files in `data/week3_manifest.json`.