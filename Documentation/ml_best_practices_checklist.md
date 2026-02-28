# ML Best Practices Checklist
> **Instructions:** Copy the section below at the top of every new notebook or script. Tick off each item before proceeding.
> Environment: `ml-ultra` | Kernel: `Python (God Mode - ml-ultra)`

---

## ✅ Section Start Checklist

Copy-paste this block at the top of every new notebook section or script and check each item:

```markdown
### [ ] Section: <NAME>  —  Checklist

#### Environment
- [ ] Kernel is set to **ml-ultra** (`Python (God Mode - ml-ultra)`)
- [ ] `torch.cuda.is_available()` returns `True` (run once per session)

#### Data Handling
- [ ] Data is loaded from `Data/processed/*.parquet` (NOT raw CSVs)
- [ ] Using **Polars** for large files (>500 MB) or **Pandas** for smaller ones
- [ ] No in-place mutation of raw data files

#### Preprocessing
- [ ] Data is scaled (StandardScaler / MinMaxScaler) if using:
  - Logistic Regression, SVM, Neural Networks, KNN
- [ ] Train/Val/Test split done BEFORE fitting any scaler

#### Model Setup
- [ ] Scikit-Learn CPU models use `n_jobs=-1`
- [ ] Logistic Regression uses `solver='saga'` for large datasets
- [ ] GPU models (PyTorch/XGBoost) confirm device = `cuda`

#### Saving Results
- [ ] Figures saved to `Results/figures/`
- [ ] Tables/metrics saved to `Results/tables/`
- [ ] Model weights saved to `Results/model_weights/`
- [ ] Full model objects saved to `Results/model_weights/` as `.pkl`
```

---

## Standard Imports Snippet

```python
# ── Standard Imports ───────────────────────────────────────────────
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from pathlib import Path
import sys
import torch

# Sklearn
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler
from sklearn.metrics import accuracy_score, roc_auc_score

# GPU check (run once)
print("CUDA available:", torch.cuda.is_available())
print("Device:", torch.cuda.get_device_name(0) if torch.cuda.is_available() else "CPU")

# Project root
ROOT = Path(__file__).resolve().parents[1]  # adjust depth as needed
DATA_DIR      = ROOT / "Data" / "processed"
RESULTS_DIR   = ROOT / "Results"
```

---

## Data Format Reference

| Format   | Use Case                        | Read Speed | Size   |
|----------|---------------------------------|------------|--------|
| `.parquet`| **Primary data store** ✅       | Fast       | Small  |
| `.feather`| Intermediate/temp data          | Very fast  | Medium |
| `.pkl`   | Saved Python objects (models)   | Fast       | Varies |
| `.csv`   | ❌ Avoid — slow, large, no types | Slow       | Large  |

---

## File Format Rules
- **Raw data** → `Data/raw/` (read-only, never overwrite)
- **Converted/cleaned** → `Data/processed/*.parquet`
- **Interim/scratch** → `Data/interim/*.feather`
- **Metadata** → `Data/metadata/`
