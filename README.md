# Conciliation
**Article reproducibility – Classification of the Conciliation Profile in Initial Petitions in the Brazilian Judiciary**

---

## CLASSIFYING-CONCILIATION-PROFILE

This repository organizes a pipeline to **classify the conciliation profile** (e.g., cases from *Anonymous*), combining:

- **Preprocessing** and feature generation (regex / tabular attributes / LLM-based features)
- **Embeddings** (e.g., Gemini / SBERT) with partial checkpointing
- **Training and evaluation** using classical models (XGBoost / RandomForest / LightGBM / CatBoost)
- A **production test version** (app + Docker) to serve the trained model

> ⚠️ **Important (LGPD / sensitive data):**  
> This repository is structured to **avoid versioning real data** (CSV/JSON), embeddings, and heavy/derived artifacts (PKL/NPY/checkpoints).  
> The documentation below clearly states what remains **local** versus what is pushed to **GitHub**.

---

## Repository structure (local view)

Below is a view of the **local environment**, including data and artifacts.  
It is provided **for organizational reference only**.

### Structure (summary)

```text
CLASSIFYING-CONCILIATION-PROFILE/
├─ Abordagem-com-LLM/
│  ├─ Dados/
│  │  └─ dados_processos_Anonymous_14052025.json
│  ├─ Conciliacao.ipynb
│  ├─ dados_processos_completo.csv
│  └─ embeddings_Anonymous.npy
├─ Analise-Estatistica-do-Corpus/
│  └─ Statistical-Corpus-Analysis.ipynb
├─ Conciliacao/
│  ├─ dados/
│  │  ├─ Anonymous_03072025/
│  │  │  ├─ dados_processos_Anonymous_03072025_V1.csv
│  │  │  ├─ dados_processos_Anonymous_03072025_V2.csv
│  │  │  ├─ dados_processos_Anonymous_03072025_V3.csv
│  │  │  ├─ dados_processos_Anonymous_03072025.json
│  │  │  ├─ dados_processos_Anonymous_03072025_V1_enriched.json
│  │  │  └─ artificial_token_frequency.txt
│  │  └─ Anonymous_14052025/
│  │     ├─ dados_processos_Anonymous_14052025_V1.csv
│  │     ├─ dados_processos_Anonymous_14052025_V2.csv
│  │     ├─ dados_processos_Anonymous_14052025_V3.csv
│  │     ├─ dados_processos_Anonymous_14052025.json
│  │     ├─ dados_processos_Anonymous_14052025_V1_enriched.json
│  │     └─ artificial_token_frequency.txt
│  └─ Embeddings/
│     ├─ partial_checkpoint.npy
│     ├─ checkpoint_partial_03072025.npy
│     ├─ dados_processos_Anonymous_14052025_rejected.json
│     ├─ dados_processos_Anonymous_14052025_V1_enriched_Embeddings.json
│     ├─ dados_processos_Anonymous_14052025_V1_enriched_Embeddings.npy
│     └─ ...
├─ Scripts/
│  ├─ gerar_embeddings_gemini.py
│  ├─ gerar_features_gemini.py
│  ├─ gerar_features_sentence_transformers.py
│  ├─ classificar_com_gemini.py
│  ├─ partial_checkpoint.json
│  └─ br-tjgo-cld-02-09b1b22e65b3.json
├─ Notebooks/
│  ├─ XGBoost/
│  │  ├─ XGBoost.ipynb
│  │  └─ xgb_classification_model.pkl
│  ├─ Random-Forest/
│  │  ├─ Random-Forest.ipynb
│  │  └─ rf_classification_model.pkl
│  ├─ LightGBM/
│  │  ├─ LightGBM.ipynb
│  │  └─ lightgbm_model_v1.pkl
│  ├─ CatBoost/
│  │  ├─ CatBoost.ipynb
│  │  ├─ CatBoost.pkl
│  │  └─ catboost_info/ (logs/artifacts)
│  ├─ pre-processing.ipynb
│  └─ regex_analysis.ipynb
└─ XGBoost-Production-Test/
   ├─ App/
   │  ├─ app.py
   │  ├─ xgboost_model.pkl
   │  ├─ randomforest_model.pkl
   │  ├─ templates/index.html
   │  └─ static/Logo.png
   ├─ Dados/
   │  ├─ Embeddings/text_vectors.npy
   │  ├─ cleaned_Anonymous_processes.json
   │  ├─ Anonymous_processes_features.json
   │  └─ dados_processos_Anonymous_14052025.csv
   ├─ Notebooks/
   │  ├─ pre-processing-data.ipynb
   │  └─ xgboost.ipynb
   ├─ Dockerfile
   ├─ docker-compose.yml
   └─ requirements.txt
```

---

## What should NOT be pushed to GitHub (and why)

### 1) Raw and derived data (LGPD / volume / reproducibility)
❌ **Do not version:**
- `**/Dados/**/*.csv`
- `**/Dados/**/*.json`
- `**/Conciliacao/dados/**`
- `**/artificial_token_frequency.txt` (if derived from real data)

✅ **Recommended approach:**
- Commit **only the folder structure** (empty folders with `.gitkeep`) and/or **an anonymized sample** (e.g., `sample_data.csv`).

---

### 2) Embeddings and vectors (heavy + derived)
❌ **Do not version:**
- `**/*.npy`
- `**/*Embeddings*.json`
- `**/*Embeddings*.npy`
- `**/Embeddings/**`

Reason: large files derived from data that do not add traceability in Git.

---

### 3) Checkpoints and partial states
❌ **Do not version:**
- `partial_checkpoint.json`
- `partial_checkpoint.npy`
- `checkpoint_partial_*.npy`
- any checkpoint files created for execution resumption

Reason: temporary, machine- and execution-specific artifacts.

---

### 4) Trained models (PKL / binaries)
❌ **Recommended not to version:**
- `**/*.pkl`
- `**/*.joblib`

Alternatives:
- publish models via a **Model Registry** or GitHub Releases
- version only the training pipeline and instructions to reproduce

---

### 5) Training logs / execution artifacts
❌ **Do not version:**
- `catboost_info/`
- `events.out.tfevents*`
- `tmp/`
- training-generated `*.tsv`
- `.ipynb_checkpoints/`
- `__pycache__/`, `*.pyc`

---

## Folders that SHOULD exist (even if empty)

To facilitate reproducibility, keep the following folders in the repository (use `.gitkeep`):

```text
Conciliacao/dados/
Conciliacao/Embeddings/
XGBoost-Production-Test/Dados/
XGBoost-Production-Test/Dados/Embeddings/
docs/   (optional, for figures/screenshots)
```

---

## Suggested `.gitignore`

```gitignore
# =========================
# DATA (LGPD / do not version)
# =========================
**/Dados/**/*.csv
**/Dados/**/*.json
**/Conciliacao/dados/**
**/dados/**/*.csv
**/dados/**/*.json

# =========================
# EMBEDDINGS / VECTORS
# =========================
**/*.npy
**/Embeddings/**
**/*Embeddings*.json
**/*Embeddings*.npy

# =========================
# CHECKPOINTS
# =========================
**/checkpoint*.json
**/checkpoint*.npy

# =========================
# TRAINED MODELS
# =========================
**/*.pkl
**/*.joblib

# =========================
# LOGS / TRAINING ARTIFACTS
# =========================
**/catboost_info/
**/events.out.tfevents*
**/tmp/
**/*.tsv

# =========================
# JUPYTER / PYTHON
# =========================
.ipynb_checkpoints/
__pycache__/
*.pyc
.venv/
.env
```

---

## How to reproduce

1. **Place the data locally** in `Dados/` or `Conciliacao/dados/Anonymous_*/`.
2. Run scripts in `Scripts/` to:
   - generate features (`gerar_features_*.py`)
   - generate embeddings (`gerar_embeddings_*.py`)
3. Use notebooks in `Notebooks/` to train and evaluate models (XGBoost / RF / LGBM / CatBoost).
4. To serve the model, use `XGBoost-Production-Test/` (Docker + app).

---

## Dataset

Dataset used in this project:  
https://huggingface.co/datasets/Anonymous/
