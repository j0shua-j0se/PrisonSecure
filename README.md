```markdown
# PRISONSECURE

**PRISONSECURE** — GPU-accelerated, edge-capable surveillance pipeline for near real-time weapon detection and identity verification.  
This `README.md` is tailored to the exact files you uploaded and is ready to paste into the root of your repository.

---

## Project snapshot — uploaded files
```

Compiled\_PJT\_prefinal.ipynb
DemoVideo.mp4
Project\_Report.pdf
alarm-KNIFE.mp3
alert-GUN.mp3
best.pt
classifier.pkl
deploy.prototxt
label\_encoder.pkl
res10\_300x300\_ssd\_iter\_140000.caffemodel

```

---

## Short description
PRISONSECURE fuses object detection (YOLO / `best.pt`), face detection (OpenCV DNN — `.prototxt` + `.caffemodel`), face embeddings, and a lightweight classifier (`classifier.pkl` + `label_encoder.pkl`) to detect weapons and verify identities. The repository includes a demonstration notebook, demo video, alert sounds and the required models for running local demos.

---

## Recommended repository layout (move files into these folders)
```

PRISONSECURE/
├─ README.md
├─ Project\_Report.pdf
├─ notebooks/
│  └─ Compiled\_PJT\_prefinal.ipynb
├─ models/
│  ├─ best.pt
│  ├─ res10\_300x300\_ssd\_iter\_140000.caffemodel
│  └─ deploy.prototxt
├─ models/pickles/
│  ├─ classifier.pkl
│  └─ label\_encoder.pkl
├─ data/sample/
│  └─ DemoVideo.mp4
├─ assets/
│  ├─ alert-GUN.mp3
│  └─ alarm-KNIFE.mp3
├─ requirements.txt
├─ scripts/
│  └─ download\_models.sh   # optional placeholder
└─ .gitignore

````

> Move files accordingly and update paths in the notebook to use the relative paths above.

---

## Quick start

### 1. Clone repo
```bash
git clone https://github.com/<your-username>/PRISONSECURE.git
cd PRISONSECURE
````

### 2. Create and activate virtual environment

**venv**

```bash
python -m venv .venv
# Linux / macOS
source .venv/bin/activate
# Windows PowerShell
.\.venv\Scripts\Activate.ps1
```

**or conda**

```bash
conda create -n prisonsecure python=3.10 -y
conda activate prisonsecure
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

> If you use a GPU, install the `torch` wheel compatible with your CUDA version first (see [https://pytorch.org/](https://pytorch.org/)).

### 4. Verify models and files are in place

Ensure the files are present relative to the repo root:

```text
models/best.pt
models/deploy.prototxt
models/res10_300x300_ssd_iter_140000.caffemodel
models/pickles/classifier.pkl
models/pickles/label_encoder.pkl
data/sample/DemoVideo.mp4
assets/alert-GUN.mp3
assets/alarm-KNIFE.mp3
notebooks/Compiled_PJT_prefinal.ipynb
```

### 5. Run the notebook demo

```bash
jupyter lab notebooks/Compiled_PJT_prefinal.ipynb
```

* Before running, open the notebook and set model paths to the relative ones above (no absolute Windows paths).
* Use `data/sample/DemoVideo.mp4` as demo input or `0` for webcam.

---

## Suggested small script (example usage)

If you refactor the notebook to a script, example CLI:

```bash
python src/run_demo.py \
  --detector models/best.pt \
  --face_proto models/deploy.prototxt \
  --face_model models/res10_300x300_ssd_iter_140000.caffemodel \
  --classifier models/pickles/classifier.pkl \
  --label_encoder models/pickles/label_encoder.pkl \
  --input data/sample/DemoVideo.mp4 \
  --alert_gun assets/alert-GUN.mp3 \
  --alarm_knife assets/alarm-KNIFE.mp3
```

---

## Configuration file (recommended)

Create `config.yaml` and point your code/notebook to it:

```yaml
model_paths:
  detector: "models/best.pt"
  face_proto: "models/deploy.prototxt"
  face_model: "models/res10_300x300_ssd_iter_140000.caffemodel"
  classifier: "models/pickles/classifier.pkl"
  label_encoder: "models/pickles/label_encoder.pkl"
assets:
  alert_gun: "assets/alert-GUN.mp3"
  alarm_knife: "assets/alarm-KNIFE.mp3"
thresholds:
  detection_conf: 0.4
  face_match: 0.6
input:
  demo_video: "data/sample/DemoVideo.mp4"
```

---

## Notes about the uploaded files

* **`best.pt`**: PyTorch/YOLO checkpoint — used for weapon/object detection.
* **`res10_300x300_ssd_iter_140000.caffemodel` + `deploy.prototxt`**: OpenCV DNN face detector (SSD Caffe model) — used for face localization.
* **`classifier.pkl` + `label_encoder.pkl`**: Pickled scikit-learn (or similar) classifier and label encoder. When loading, use the same `scikit-learn` version used to create them, or re-train if incompatible.
* **`Compiled_PJT_prefinal.ipynb`**: Notebook containing demo and pipeline; replace absolute paths with the relative ones above.
* **`DemoVideo.mp4`**: Demo footage for testing.
* **`alert-GUN.mp3`, `alarm-KNIFE.mp3`**: Demo alert sounds. Use cross-platform audio player libraries (`simpleaudio`, `playsound`, etc.) for playback.
* **`Project_Report.pdf`**: Final report for reviewers / documentation.

---

## Git & large files

Model binaries can bloat a git repo. Two options:

**1) Use Git LFS**

```bash
git lfs install
git lfs track "models/*.pt"
git lfs.track "models/*.caffemodel"
git lfs track "models/pickles/*.pkl"
git add .gitattributes
git add models/...
git commit -m "Add models via git-lfs"
git push
```

**2) Host externally and provide a download script**
Create `scripts/download_models.sh`:

```bash
#!/usr/bin/env bash
mkdir -p models models/pickles data/sample assets
# example:
# wget -O models/best.pt "<MODEL_URL>"
# wget -O models/res10_300x300_ssd_iter_140000.caffemodel "<MODEL_URL>"
# wget -O models/deploy.prototxt "<MODEL_URL>"
# wget -O models/pickles/classifier.pkl "<MODEL_URL>"
# wget -O models/pickles/label_encoder.pkl "<MODEL_URL>"
```

---

## Troubleshooting

* **Model load error**: verify paths and library versions. For `torch` issues, confirm CUDA / driver compatibility.
* **Pickle load error**: pickles may require the same Python and `scikit-learn` versions. If incompatible, re-create pickles with the current environment.
* **Audio playback fails**: ensure the Python audio library used (e.g., `simpleaudio`) is installed and that the runtime environment supports audio.
* **Notebook too large**: clear outputs before committing (`Kernel → Restart & Clear Output`) or use `nbstripout`.

---

## Reproducibility & evaluation

* Store experiment configuration (`config.yaml`), seeds, and model versions.
* Evaluate detection with mAP and recognition with accuracy / ROC / FAR/FRR.
* Measure latency to estimate edge performance (target depends on hardware).

---

## Ethics & deployment considerations

* Follow local privacy laws and institutional approval procedures before deploying face recognition.
* Minimize retention of raw face images — prefer embeddings and hashed IDs.
* Maintain access control and audit logs.

---
