PRISONSECURE is a GPU-accelerated, edge-capable surveillance framework designed to detect weapons and verify identities in correctional environments in (near) real time. The system combines a one-stage object detector (YOLO-style), a face-embedding model (FaceNet-style), and a lightweight classifier to produce fast, interpretable alerts for security personnel. The codebase and experiments were developed to be modular, reproducible, and easy to deploy on resource-constrained devices (e.g., NVIDIA Jetson family, GPU-enabled edge servers).

### Key goals

* **Real-time threat detection:** Detect weapons and suspicious objects in video streams with minimal latency.
* **Accurate identity verification:** Match detected faces to a curated inmate database to enable context-aware alerts (e.g., flagging restricted interactions).
* **Edge-friendly:** Prioritize models and engineering patterns that run on edge devices with limited compute while still leveraging GPU acceleration when available.
* **Reproducible pipeline:** Provide scripts and environment manifests for reproducible experiments and deployment.

### System architecture (component-level)

1. **Input sources**

   * CCTV cameras, RTSP streams, or local video files.
   * Optional: batch-processing from recorded footage.

2. **Preprocessing**

   * Frame extraction, resizing, basic normalization.
   * Optional augmentation for offline training.

3. **Object detection (YOLO)**

   * A YOLO-derived detector detects objects (weapons, handheld items, etc.).
   * Produces bounding boxes, class scores, and object-level confidence.

4. **Face detection & embedding**

   * Face detector crops faces inside frames (lightweight MTCNN / face detector).
   * Face embeddings computed using a FaceNet-style model to produce 128–512D vectors.

5. **Identity & weapon classification**

   * A classifier (SVM / small MLP) maps embedding vectors to known identities.
   * A simple heuristic or learned fusion module correlates weapon detections with person bounding boxes.

6. **Alerting & logging**

   * Alert messages (JSON) are output when thresholds are crossed.
   * Logs saved to disk or forwarded to a monitoring endpoint. Optional integration with messaging channels.

7. **Dashboard / visualization (optional)**

   * Lightweight web UI for viewing live detections, reviewing flagged clips, and managing the identity database.

### Data & models

* **Models used**

  * YOLO-style detector (`best.pt`) — used for object detection.
  * Face embedding model (`keras-facenet` or `facenet-pytorch`) — for identity vectors.
  * Classifier (`classifier.pkl`), label encoder (`label_encoder.pkl`) — for mapping embeddings to known IDs.
* **Data**

  * Use private, curated datasets consistent with institutional policy. Provide a small sample set (`data/sample/`) for testing.
  * For training or evaluation, follow responsible data handling procedures (see Ethical Considerations below).

### Performance & evaluation

* **Latency target:** < 200 ms per frame on GPU-capable edge device (actual latency depends on hardware).
* **Evaluation metrics**

  * Object detection: mAP\@0.5, precision, recall.
  * Face recognition: top-1 accuracy, ROC / AUC, false acceptance rate (FAR), false rejection rate (FRR).
  * System-level: end-to-end detection-to-alert latency, precision/recall for threat alerts.
* **Reproducibility**

  * Include `scripts/` for running smoke tests and sample evaluation scripts.
  * Save experiment configs (`config.yaml`) and seed random number generators for deterministic evaluation runs.

### Limitations

* Datasets used may not represent all lighting, occlusion, or camera-angle conditions found in real facilities.
* Face recognition performance drops with heavy occlusion or low-resolution cameras.
* Models require periodic re-training and validation to remain effective as conditions change.
* Legal and privacy constraints in different jurisdictions may limit practical deployment or require consent/approvals.

### Deployment & scalability

* **Small-scale:** Deploy as a single process on a Jetson/edge device with local storage and alert forwarding.
* **Large-scale:** Use containerized microservices (one process per function: detection, embedding, alerting), with a message broker (RabbitMQ / Kafka) for scale-out and fault tolerance.

### Safety, ethics & privacy

* **Purpose limit:** Designed for institutional security in correctional settings—use must follow legal, regulatory, and ethical guidelines.
* **Data minimization:** Only store embeddings or face-metadata that are strictly necessary. Prefer hashed IDs and avoid storing raw images long-term unless required.
* **Access control:** Restrict access to model outputs, logs, and identity data to authorized personnel only.
* **Bias & fairness:** Test models across demographic groups; maintain a process for reviewing false positives/negatives and remediation.
* **Auditability:** Keep reproducible logs and model versions for auditing and compliance.

### Future work & improvements

* Integrate multi-camera re-identification to track individuals across non-overlapping cameras.
* Add continual learning modules to incrementally update classifiers without full retraining.
* Add adversarial robustness checks (e.g., occlusion, spoofing).
* On-device model quantization (INT8) for faster inference on constrained hardware.

* convert this into a short one-page `docs/EXTENDED_DESCRIPTION.md`, or
* create a diagram (SVG/PNG) of the architecture suitable for the README. Which would you prefer?
