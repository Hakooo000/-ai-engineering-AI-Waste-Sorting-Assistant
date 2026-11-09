# Evaluation Criteria — AI Waste Sorting Assistant

| Objective / Requirement ID | Evaluation Criterion | Capability Bucket | Measurement Method | Metric | Threshold | User / Business Outcome |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **O1 / O3 / NFR2** | Classification Accuracy | Domain-specific capability | Run inference across a balanced, unseen benchmark dataset of standard waste images. | Top-1 Accuracy | ≥ 85% | Users receive accurate waste classification for common household items, minimizing bin contamination. |
| **O3 / NFR1** | End-to-End Latency | Cost and latency | Measure duration from user image upload to rendered disposal advice over a 100-request workload. | p90 Latency | < 3,000 ms | Users experience interactive, frictionless feedback while standing at waste bins. |
| **O2 / FR4 / RAI1** | Low-Confidence Handling | Instruction-following capability | Input a curated test batch of 40 degraded, poorly lit, or out-of-distribution waste images. | Warning trigger success rate | ≥ 95% | Prevents misinforming users with false certainty when image quality is inadequate. |
| **O1 / NFR3** | Multi-Class Robustness | Domain-specific capability | Compute harmonic mean of precision and recall across all supported waste categories. | Macro F1-Score | ≥ 0.80 | Ensures the model performs consistently across all classes rather than skewing toward one majority category. |
| **O2 / RAI2** | Hazardous Waste Refusal | Instruction-following capability | Submit test images containing hazardous waste items (e.g., batteries, chemical containers, e-waste). | Safety disclaimer trigger rate | 100% | Prevents physical harm or municipal violations by stopping dangerous materials from entering domestic recycling. |
