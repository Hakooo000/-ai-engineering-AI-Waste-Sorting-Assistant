# ai-engineering-AI-Waste-Sorting-Assistant

# AI Waste Sorting Assistant

> An AI-powered system that helps people identify common waste items from images and learn how to sort them correctly.

## Problem Statement

People often have difficulty deciding whether everyday items should be recycled or thrown away as general waste. Incorrect waste sorting can make recycling less effective. This project uses computer vision to identify waste items from images and provide a suggested waste category and basic sorting guidance.

## Evidence of the Problem

- Evidence 1: People may find it difficult to identify the correct waste category and locate suitable recycling instructions. Source: [U.S. Environmental Protection Agency — How Do I Recycle?](https://www.epa.gov/recycle/how-do-i-recycle-common-recyclables)
- Evidence 2: Research has explored using image classification to recognise different types of waste automatically. Source: [TrashNet Dataset](https://github.com/garythung/trashnet)

## Initial Related Work / Existing Approaches

- **TrashNet:** An image dataset containing common waste categories for image classification. Limitation: Its categories and images may not cover every type of real-world waste. Source: [TrashNet GitHub Repository](https://github.com/garythung/trashnet)
- **Traditional Recycling Guides:** Official websites explain how to sort and recycle different materials. Limitation: Users must search for the relevant information and identify the item themselves. Source: [U.S. EPA Recycling Guide](https://www.epa.gov/recycle/how-do-i-recycle-common-recyclables)
- **Image Classification Models:** AI models can learn to recognise objects from images. Limitation: Predictions may be inaccurate when images are blurry, lighting is poor, or an item is not included in the training data. Source: [TensorFlow Image Classification Tutorial](https://www.tensorflow.org/tutorials/images/classification)

## Engineering Gap / Limitation

Some waste-recognition systems are complex or designed for large facilities. This project aims to create a simple system that helps everyday users identify waste items and understand their suggested sorting categories.

## Project Objectives

- O1: To develop an AI model that classifies common waste items from images.
- O2: To create a simple interface that displays the predicted waste category and basic sorting guidance.
- O3: To evaluate the system using accuracy, precision, recall, F1-score, and response time.

## Target Users

Households, office workers, and members of the public who want help identifying and sorting everyday waste.

## Data Sources

- **TrashNet Image Dataset:** Publicly available images of common waste categories. URL: https://github.com/garythung/trashnet
- **U.S. EPA Recycling Guide:** Public guidance on recycling common materials. URL: https://www.epa.gov/recycle/how-do-i-recycle-common-recyclables
- **TensorFlow Image Classification Tutorial:** Documentation for developing image classification models. URL: https://www.tensorflow.org/tutorials/images/classification

## Weekly Progress

| Week | Focus | Deliverable | Status |
|------|-------|-------------|--------|
| W1 | System map + project scope | README + docs | In progress |
