# Architecture Decision Record (ADR) & Traceability Matrix — AI Waste Sorting Assistant

## Architecture Decision Records (ADR)

### Decision 1: Decoupling Disposal Guidance from the Vision Classifier
* **Requirement / Constraint:** FR3, FR5, NFR4 (Maintainability). Disposal advice must be accurate, editable, and clear without requiring model retraining.
* **Alternatives Considered:** 
  1. Multimodal LLM generating custom disposal instructions on each call.
  2. Direct classification output tied to a static, decoupled disposal knowledge configuration.
* **Selected Option:** Decoupled Disposal Guidance Knowledge Base mapped directly to predicted class IDs.
* **Trade-off Accepted:** Instructions are standardized rather than dynamically tailored, but responses are deterministic, risk of hallucinated disposal instructions is eliminated, and latency/cost remain minimal.

### Decision 2: Implementation of a Confidence Thresholding / Scope Filter
* **Requirement / Constraint:** FR4, RAI1 (Grounding / Transparency), RAI2 (Out-of-Scope Safety). The system must avoid presenting ungrounded guesses as facts.
* **Alternatives Considered:** 
  1. Always outputting the top-1 predicted class regardless of softmax score.
  2. Introducing an explicit Confidence & Scope Verification component before displaying results.
* **Selected Option:** Confidence & Scope Verification component evaluating prediction logits against a strict confidence threshold ($\tau = 0.70$).
* **Trade-off Accepted:** Slightly higher false-rejection rate on borderline images, but drastically reduces misclassification and recycling contamination risk.

---

## Requirements Traceability Matrix

| Objective | Requirement | Evaluation Criterion | Architecture Element | Later Evidence |
| :--- | :--- | :--- | :--- | :--- |
| **O1** | NFR2 (Classification Correctness) | Classification Accuracy | Image Classification Model Service | Confusion matrix and accuracy scores on unseen test split (Target: $\ge 85\%$). |
| **O2** | FR3, FR4 (Disposal Guidance & Confidence Gate) | Low-Confidence Handling & Output Rendering | Application Logic & Scope/Confidence Filter | Test suite verification ensuring warnings fire on inputs with confidence $< 0.70$. |
| **O3** | NFR1 (Response Latency) | End-to-End Latency | Application Interface to Model Pipeline | Latency benchmark report measuring p90 execution time under test load (Target: $< 3\text{ s}$). |
| **O1 / O2** | RAI2 (Safety / Scope Refusal) | Hazardous Waste Refusal | Scope & Safety Control Module | Safety validation report evaluating system refusal against hazardous items (Target: $100\%$). |
