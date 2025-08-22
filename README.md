# NeverMind — Internal README  
⚠️ **CONFIDENTIAL — INTERNAL USE ONLY**  
Do **not** distribute, publish, or upload this project, its documentation, or related data outside the approved team. This repository is intended solely as a private R&D workspace.  

---

## Purpose  
NeverMind is an internal research prototype designed to explore **non-invasive thought-to-speech conversion** using EEG. This README is the **master reference** for internal contributors, detailing project scope, architecture, protocols, experiment guidelines, security rules, and operational policies.  

---

## Evaluation, metrics & reporting

### Primary metrics  
- **Accuracy (Top-1, Top-3):** Correct classification of imagined speech phrases.  
- **Confusion matrix:** To analyze misclassifications (e.g., digit “two” confused with “three”).  
- **Latency:** Measured from EEG capture → speech playback; target ≤ 1000 ms.  
- **Usability metric:** Subjective intelligibility and user comfort during trials.  

### Secondary metrics  
- **F1 score (macro/weighted):** Especially relevant for class imbalance.  
- **Cosine similarity:** If mapping EEG to speech embedding space.  
- **Top-k retrieval:** Accuracy of finding the correct phrase in top-k predictions.  
- **Words per minute (WPM):** Effective communication rate.  

### Reporting standards  
- Each experiment must produce:  
  - `metrics.json` (accuracy, F1, latency, parameters).  
  - A training log with timestamp, seed, commit hash.  
  - Confusion matrix plot in `/reports/plots/`.  
- Summaries should be stored in `/reports/` and tagged with experiment ID.  

---

## Reproducibility, logging & experiment tracking

- **Experiment IDs:** Use format `exp-YYYYMMDD-shortname-subjectID`.  
- **Logging:** Use Python `logging` module (INFO level by default; DEBUG for dev runs).  
- **Tracking:** Store configs and metrics in `/experiments/`. Suggested tools: MLflow, Weights & Biases (local-only mode).  
- **Random seeds:** Log all seeds to ensure reproducibility.  
- **Environment capture:** Freeze dependencies with `requirements.txt` or `conda env export`.  

---

## Deployment & real-time demo (internal use only)

The prototype must allow internal demo of **live EEG → speech playback**:  
1. Acquisition server streams EEG (SDK or LSL).  
2. Preprocessing buffer runs in sliding windows (0.5–2s).  
3. Model inference runs in <200 ms per window.  
4. Decoder passes result to **TTS engine** (Tacotron, Coqui, or lightweight parametric).  
5. Audio playback + live transcript on GUI.  

**Command example:**  
```bash
python src/ui/live_demo.py   --device COM3   --model checkpoints/model_v1.pth   --config conf/demo_config.json   --output_speaker True
```

---

## Security, access control & audit requirements

- **Access:** Repo is private. Contributors require explicit approval from project lead.  
- **Authentication:** Use 2FA on all accounts connected to this project.  
- **Data:** Raw EEG and PII must never enter the repo. Store in offline encrypted drives or restricted cloud buckets.  
- **Encryption:** AES-256 for all backups; SSH/SFTP only for transfers.  
- **Audit:** Keep access logs for sensitive data. Review quarterly.  
- **PII stripping:** Anonymize all subject info — only use IDs (e.g., `S001`).  

---

## Backup, retention & disposal policy

- **Backups:**  
  - Raw data: 3-2-1 rule (3 copies, 2 storage mediums, 1 offsite encrypted).  
  - Code & repo: private GitHub + local mirror.  

- **Retention:**  
  - Raw EEG: 5 years minimum (aligned with research standards).  
  - Processed/derived data: 3 years unless project continues.  

- **Disposal:**  
  - Shred (digital wipe) storage devices before disposal.  
  - Verify deletion with checksum mismatch confirmation.  

---

## Repository layout & file conventions

```
/NeverMind
├─ README.md              <-- This file (internal use only)
├─ docs/                  <-- Protocols, internal reports, feasibility notes
├─ src/                   <-- Preprocessing, models, TTS, UI
│  ├─ acquisition/
│  ├─ preprocess/
│  ├─ models/
│  └─ tts/
├─ scripts/               <-- Helper scripts
│  ├─ collect.py
│  ├─ preprocess.py
│  ├─ train.py
│  └─ demo.py
├─ notebooks/             <-- Experiments, exploration
├─ experiments/           <-- Logs, configs, metrics
├─ reports/               <-- Plots, results, internal summaries
├─ conf/                  <-- JSON/YAML configs
├─ requirements.txt
└─ .gitignore             <-- Exclude data & sensitive files
```

**Never commit:**  
- Raw EEG data (`*.edf`, `*.bdf`, etc.).  
- PII metadata.  
- Consent forms or regulatory docs.  

---

## Environment & setup (example)

### Create environment
```bash
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

### Example run (offline training)
```bash
python scripts/train.py   --data data/processed/S001_session1.npz   --model cnn_spectrogram   --epochs 50   --out checkpoints/model_cnn_v1.pth
```

### Example run (real-time demo)
```bash
python scripts/demo.py   --device /dev/ttyUSB0   --model checkpoints/model_cnn_v1.pth   --tts_engine coqui
```

---

## Roles, responsibilities & contribution guidelines

- **Project Lead (Harsh):** Originator of project, final reviewer of protocols and experiments.  
- **Data handlers:** Only approved researchers with ethics clearance may handle raw EEG data.  
- **Developers:** Work only on anonymized or synthetic data.  
- **Contributors:** Must document all changes in PRs and reference related experiment IDs.  

**Contribution rules:**  
- Use feature branches (`feature/model-improvement`).  
- No direct commits to `main` without review.  
- Document each PR with: purpose, affected modules, test results.  
- Keep commit messages clear and timestamped.  

---

## Onboarding checklist for new researchers

1. **Confidentiality agreement signed** (before repo access).  
2. **Read this README fully** (especially data-handling sections).  
3. **Setup environment** (`requirements.txt`, test sample data).  
4. **Review ethics protocol** and experiment standards.  
5. **Request dataset access** via project lead (offline vault).  
6. **Run onboarding experiment** (synthetic EEG to TTS demo).  
7. **Follow contribution guidelines** for all future work.  

---

## References & notes
- Feasibility document (internal PDF in `/docs/`).  
- EEG preprocessing guide: MNE-Python docs.  
- TTS baseline: Coqui TTS or ESPnet models.  
- Security compliance: Institutional data protection guidelines.  

---

# Confidentiality Statement  
This project is **strictly private**. All code, documents, and data are confidential intellectual property of the project originator (Harsh). No redistribution or disclosure is permitted without explicit written approval.  
