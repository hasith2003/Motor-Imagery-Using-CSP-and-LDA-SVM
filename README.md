# EEG Motor Imagery Classification

A complete signal processing and machine learning pipeline for classifying left vs. right hand motor imagery from 64-channel EEG data, using Common Spatial Patterns (CSP) with LDA and SVM classifiers.

---

## 📋 Overview

This project implements an end-to-end workflow for binary motor imagery classification. Raw EEG signals are preprocessed with bandpass filtering, ICA-based artifact removal, and average referencing before being segmented into epochs. CSP extracts spatially filtered features, which are then fed into two classifiers — LDA and SVM — evaluated under 5-fold stratified cross-validation.

---

## 🎯 Dataset

| Property | Value |
|---|---|
| Source | [EEG Motor Movement/Imagery Dataset v1.0.0](https://physionet.org/content/eegmmidb/1.0.0/) |
| Runs used | R04, R08, R12 (concatenated) |
| Channels | 64 EEG electrodes (international 10-20 system) |
| Epochs | 45 trials × 3 seconds |
| Task | Binary classification — Left hand (T1) vs. Right hand (T2) |
| File format | EDF (European Data Format) |

---

## 🧹 Preprocessing Pipeline

The following steps are applied in order before feature extraction:

1. **Concatenation** — Runs 4, 8, and 12 loaded and concatenated via `mne.concatenate_raws`
2. **Channel renaming** — Standardized to MNE-compatible 10-20 naming convention
3. **Average referencing** — Re-referenced to the common average
4. **Bandpass filtering** — 8–30 Hz (mu + beta bands), zero-phase FIR filter (Hamming window, 265 samples)
5. **ICA artifact removal** — 20 ICA components fitted; EOG artifacts detected via Fp1 correlation and removed
6. **Epoching** — Segmented around T1/T2 event markers, 3 s per trial, no baseline correction
7. **Final shape** — `X: (45, 64, 481)`, `y: (45,)`

---

## 🧠 Models & Results

### Model 1: CSP + LDA

| Setting | Value |
|---|---|
| CSP regularization | Ledoit-Wolf |
| Optimal n_components | **4** |
| CV Accuracy | **0.844 ± 0.113** |
| Per-fold scores | [0.889, 0.778, 0.667, 0.889, 1.000] |

### Model 2: CSP + SVM

| Setting | Value |
|---|---|
| CSP regularization | Ledoit-Wolf |
| Optimal n_components | **8** |
| Feature scaling | StandardScaler |
| SVM kernel | RBF (C=1.0, gamma='scale') |
| CV Accuracy | **0.844 ± 0.089** |
| Per-fold scores | [0.778, 0.889, 0.778, 0.778, 1.000] |

Both models tie at **84.4% mean accuracy**. SVM achieves lower variance across folds (±0.089 vs ±0.113), suggesting slightly more stable generalization. Component sweep tested over `[2, 4, 6, 8, 10, 12]` for both models independently.

---

## 📊 Analysis & Visualization

- 2D and 3D electrode position maps
- Multi-channel EEG waveform inspection (per-trial)
- CSP component count sweep with accuracy curves
- Topographic maps of learned CSP spatial patterns
- Confusion matrices (aggregated over all CV folds) for both models

---

## 🗂️ Project Structure

```
.
├── motor.ipynb          # Main analysis notebook
├── README.md            # Project documentation
└── requirements.txt     # Python dependencies
```

> **Note:** EEG data files are not included in this repository. See the [Download the dataset](#2-download-the-dataset) section in Quick Start.

---

## 🚀 Quick Start

### 1. Clone & set up environment

```bash
git clone https://github.com/hasith2003/eeg-motor-imagery-classification.git
cd eeg-motor-imagery-classification

python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate

pip install -r requirements.txt
```

### 2. Download the dataset

The EEG data is **not included in this repository** and must be downloaded separately.

1. Go to [PhysioNet — EEG Motor Movement/Imagery Dataset v1.0.0](https://physionet.org/content/eegmmidb/1.0.0/)
2. Create a free PhysioNet account if you don't have one
3. Download the EDF files for the desired subject (runs R04, R08, R12)
4. Place them in a local folder of your choice:

```
/your/local/path/<SxxxR04.edf>
/your/local/path/<SxxxR08.edf>
/your/local/path/<SxxxR12.edf>
```

5. Update the `subject_folder` variable at the top of the notebook to point to your local path:

```python
subject_folder = r"/your/local/path/S007"
```

### 3. Run the notebook

```bash
jupyter notebook motor.ipynb
```

---

## 📦 Dependencies

```
mne>=1.0.0
scikit-learn>=1.0.0
numpy>=1.20.0
pandas>=1.3.0
matplotlib>=3.4.0
seaborn>=0.11.0
jupyter>=1.0.0
ipykernel>=6.0.0
```

Install with:
```bash
pip install -r requirements.txt
```

> Developed and tested on **Python 3.11.9**.

---

## 🔍 Key Findings

- Both CSP+LDA and CSP+SVM achieve identical mean accuracy (84.4%) but differ in their optimal component count — LDA peaks at 4 components, SVM at 8
- The accuracy–component relationship is non-monotonic for both models, confirming that more spatial filters do not always improve discrimination
- SVM exhibits lower cross-fold variance, making it preferable if deployment stability matters
- Learned CSP patterns show expected contralateral motor cortex localization, consistent with established BCI literature

---

## 🛠️ Technologies

**Signal processing & EEG:** `mne-python` — preprocessing, ICA, epoching, CSP, topographic visualization

**Machine learning:** `scikit-learn` — LDA, SVM, stratified K-fold CV, confusion matrix analysis

**Numerics & visualization:** `numpy`, `pandas`, `matplotlib`, `seaborn`

---

## 📚 References

- Blankertz, B., et al. (2008). Optimizing spatial filters for robust EEG single-trial analysis. *IEEE Signal Processing Magazine*, 25(1), 41–56.
- Goldberger, A., et al. (2000). PhysioBank, PhysioToolkit, and PhysioNet. *Circulation*, 101(23).
- [MNE-Python Documentation](https://mne.tools/)
- [Scikit-learn Pipeline Guide](https://scikit-learn.org/stable/modules/pipeline.html)

---

## 👤 Authors

[@hasith2003](https://github.com/hasith2003) · [@chalee88](https://github.com/chalee88)

## 📝 License

MIT License — see `LICENSE` for details.

---

**Status:** Active Development &nbsp;|&nbsp; **Last Updated:** 2024
