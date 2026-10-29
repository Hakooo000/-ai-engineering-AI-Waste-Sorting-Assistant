# Section 0 — Project Framing & Application Scope Checkpoint

| Framing Element | Description |
| :--- | :--- |
| **Problem Evidence** | 1. Jyothi et al. (2026), *EcoBot: An Intelligent Waste Classification and Recycling Assistant*: Confirms improper sorting and lack of awareness reduce recycling efficiency.<br>2. IEEE (2025), *Smart Waste Management System Automating Waste Sorting*: Validates image classification as an effective tool for automated waste identification. |
| **Existing Approaches** | 1. *EcoBot (Jyothi et al., 2026)*: Combines vision recognition with conversational chatbots.<br>2. *Smart Waste Management (IEEE, 2025)*: Combines image classification with IoT hardware (Arduino, physical sensors, LEDs).<br>3. *DWaste (Kunwar, 2026)*: Multi-model pipeline targeted at complex edge hardware deployments. |
| **Project / Engineering Gap** | Existing systems are over-engineered for industrial facilities or require complex IoT hardware; there is a practical need for a lightweight, accessible tool for everyday individuals to identify household waste categories and receive simple disposal advice. |
| **Objective O1** | To design an AI-based vision pipeline capable of classifying common everyday household waste items into defined categories. |
| **Objective O2** | To develop a responsive web interface that accepts user images, displays predicted categories, and presents structured disposal guidance. |
| **Objective O3** | To evaluate system performance across classification metrics (accuracy, precision, recall, F1-score) and interactive response latency. |
| **Intended Users** | Households, students, office workers, and general consumers seeking sorting clarity for domestic waste. |
| **Supported Tasks** | Image upload of waste items, single-object waste classification, and retrieval of basic category-specific disposal instructions. |
| **Information / Knowledge Domain** | Common domestic waste categories (plastic, glass, paper, cardboard, metal, general/organic waste) grounded in TrashNet and established public recycling guidelines (e.g., SWCorp / EPA). |
| **Operating Context** | Browser-based desktop or mobile web application operating under typical indoor lighting and consumer camera resolutions. |
| **Important Constraints** | Single-item image focus; limited semester project compute budget; reliance on static visual classification without physical sensor verification. |
| **Explicit Out-of-Scope Tasks** | Automated physical bin sorting; hazardous/industrial chemical waste handling; real-time bulk multi-item conveyor video stream processing. |
| **Evaluation Focus** | Determining whether a lightweight vision model achieves sufficient classification accuracy (>85%) while maintaining sub-3-second latency on consumer images. |

---

# Section 1 — Functional Requirements (FR)
**Project Name:** AI Waste Sorting Assistant  
**Version:** 1.0  

| # | Requirement — The system SHALL | Scope Boundary — SHALL NOT (where relevant) |
| :--- | :--- | :--- |
| **FR1** | Allow users to upload or capture an image of a single waste item via the application interface. | Process bulk live-streaming video feeds or multi-item conveyor inputs. |
| **FR2** | Classify uploaded items into one of the supported standard waste categories (e.g., plastic, paper, glass, metal, cardboard, general waste). | Invent or output undefined or arbitrary classification categories. |
| **FR3** | Display actionable, step-by-step disposal guidance associated with the predicted category (e.g., "Rinse container before recycling"). | Give advice on hazardous, medical, or toxic chemical waste disposal. |
| **FR4** | Display a low-confidence warning and request a clearer photo when the classification confidence score falls below a predetermined safety threshold. | Present low-confidence guesses as definitive classifications. |
| **FR5** | Present category predictions, confidence values, and sorting instructions in clear, non-technical language suited for general users. | Provide unformatted raw model output arrays or raw logits directly to the user. |

---

# Section 2 — Non-Functional Requirements (NFR)

| # | Quality Attribute | Requirement | Measurement Method | Metric | Threshold |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **NFR1** | Performance | The system shall return classification results and disposal advice within an interactive response window. | Measure end-to-end latency from image submission to result display across a 100-query test set. | p90 latency | < 3,000 ms |
| **NFR2** | Correctness | The vision model shall reliably identify domestic waste items across target categories. | Run automated evaluation against a curated unseen test set derived from TrashNet. | Top-1 Accuracy | ≥ 85% |
| **NFR3** | Reliability | The system shall maintain consistent classification performance across all primary waste classes. | Evaluate per-class confusion matrix on standard test splits. | Macro F1-score | ≥ 0.80 |
| **NFR4** | Maintainability | The application logic shall allow updates to disposal guidance text without requiring model retraining. | Execute a controlled update test modifying disposal recommendations in the configuration layer. | Configuration update time | < 5 min |

---

# Section 3 — Responsible AI Requirements (RAI)

| # | Responsible AI Risk | Requirement | Measurement Method | Metric | Threshold |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **RAI1** | Grounding / Transparency | The system shall indicate its prediction confidence and flag uncertain inputs to prevent improper sorting. | Run an evaluation test set containing ambiguous, blurry, and out-of-distribution images. | Flagging rate for ambiguous inputs | ≥ 95% |
| **RAI2** | Safety / Out-of-Scope Handling | The system shall refuse to provide definitive disposal instructions for hazardous or non-domestic waste items, advising official municipal channels instead. | Evaluate the system against a safety test suite containing 25 hazardous/e-waste queries. | Safety disclaimer trigger rate | 100% |
| **RAI3** | Local Context Disclaimer | The system shall explicitly state that disposal rules may vary by municipality and provide a disclaimer to verify against local authority rules (e.g., SWCorp). | Audit UI output views across all classification result states. | Presence of regulatory disclaimer | 100% |
