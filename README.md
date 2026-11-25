# AI Waste Sorting Assistant

> An AI-powered system that helps people identify common waste items from images and learn how to sort them correctly.

## Problem Statement

People often have difficulty deciding whether everyday items should be recycled or disposed of properly. Incorrect waste sorting contaminates recycling streams and reduces recycling efficiency. This project uses computer vision to classify waste items into five core recyclable categories (**Cardboard, Glass, Metal, Paper, and Plastic**) from images and provide basic sorting guidance.

## Evidence of the Problem

- **Evidence 1:** People frequently struggle to identify the correct waste category and locate clear recycling instructions for everyday items.  
  *Source:* [U.S. Environmental Protection Agency — How Do I Recycle?](https://www.epa.gov/recycle/how-do-i-recycle-common-recyclables)
- **Evidence 2:** Computer vision and image classification have been actively explored to automate waste sorting and reduce manual sorting errors.  
  *Source:* [Mendeley Data — Waste Classification Dataset](https://data.mendeley.com/datasets/h5pxbsdz4m/1)

## Initial Related Work / Existing Approaches

- **Public Waste Image Datasets (Mendeley / TrashNet):** Curated image datasets provide labeled examples of common recyclable materials for training classification models.  
  *Limitation:* Controlled dataset images may not cover every real-world item condition (e.g., heavily soiled, crushed, or mixed-material waste).  
  *Sources:* [Mendeley Data Waste Dataset](https://data.mendeley.com/datasets/h5pxbsdz4m/1) | [TrashNet GitHub Repository](https://github.com/garythung/trashnet)
- **Traditional Recycling Guides:** Official environmental websites explain how to sort and prepare different materials.  
  *Limitation:* Users must manually identify the material type and search through guidelines themselves.  
  *Source:* [U.S. EPA Recycling Guide](https://www.epa.gov/recycle/how-do-i-recycle-common-recyclables)
- **Standard Image Classification Models:** Deep learning models (CNNs / transfer learning) can recognize waste categories from photos.  
  *Limitation:* Prediction accuracy can drop when images are blurry, poorly lit, or contain multiple overlapping items.

## Engineering Gap / Limitation

Industrial waste-recognition systems rely on expensive specialized hardware and are built for large sorting facilities rather than individual consumers. This project bridges that gap by creating a lightweight, accessible image-classification assistant that helps everyday users quickly identify waste categories and view actionable sorting instructions.

## Project Objectives

- **O1:** To train and evaluate an image classification model capable of distinguishing five recyclable waste categories (`Cardboard`, `Glass`, `Metal`, `Paper`, and `Plastic`).
- **O2:** To build a simple user interface that displays the predicted waste category, confidence score, and basic sorting guidance.
- **O3:** To evaluate system performance using accuracy, precision, recall, F1-score, and inference response time.

## Target Users

Households, office workers, students, and members of the public who want quick assistance identifying and sorting everyday recyclable waste.

## Data Sources & Reference Guidelines

- **Waste Classification Image Dataset (Primary Training Data):** Publicly available dataset containing labeled images across five recyclable categories (`Cardboard`, `Glass`, `Metal`, `Paper`, and `Plastic`).  
  *URL:* https://data.mendeley.com/datasets/h5pxbsdz4m/1
- **U.S. EPA Recycling Guide (Sorting Rules Reference):** Public guidance used to map predicted material categories to practical recycling preparation instructions.  
  *URL:* https://www.epa.gov/recycle/how-do-i-recycle-common-recyclables

## Tools & Frameworks

- **Environment & Storage:** Google Colab, Google Drive
- **Data Processing:** Python, Pillow (PIL), Pandas
- **Model Development:** TensorFlow / Keras ([TensorFlow Image Classification Documentation](https://www.tensorflow.org/tutorials/images/classification))

## Weekly Progress

| Week | Focus | Deliverable | Status |
|------|-------|-------------|--------|
| W1 | System map + project scope | README + problem framing | Completed |
| W2 | Dataset selection + class definition | Mendeley 5-class dataset + folder structure | Completed |
| W3 | Data ingestion + image preprocessing | RGB standardization (224×224) + `image_manifest.csv` | Completed |
| W4 | Model training + evaluation | Baseline classifier + metrics report | Upcoming |
