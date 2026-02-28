# 🚀 Project Launch Checklist
> **When to use**: Run through this top-to-bottom at the start of every work session.
> **Project**: Psy Med Deterioration Window
> **Root**: `D:\source\repos\Psy_med_detoriation_window\`

---

## STAGE 1 — Environment ✅

| # | Check | Command / Action | Expected Result |
|---|-------|-----------------|-----------------|
| 1 | Kernel selected | Antigravity IDE → Kernel selector | `Python (God Mode - ml-ultra)` |
| 2 | Python version | `import sys; print(sys.version)` | `3.11.14` |
| 3 | CUDA available | `import torch; print(torch.cuda.is_available())` | `True` |
| 4 | GPU name | `print(torch.cuda.get_device_name(0))` | Your GPU name |
| 5 | Polars import | `import polars as pl; print(pl.__version__)` | No error |

```python
# Paste at top of every notebook — Stage 1 verification
import sys, torch, polars as pl
assert sys.version_info[:2] == (3, 11), f"Wrong Python: {sys.version}"
assert torch.cuda.is_available(), "CUDA not available — check drivers!"
print(f"✅ Python {sys.version.split()[0]}  |  GPU: {torch.cuda.get_device_name(0)}  |  Polars {pl.__version__}")
```

---

## STAGE 2 — Project Structure ✅

Run this once to confirm all folders exist:

```python
from pathlib import Path
ROOT = Path(r"D:\source\repos\Psy_med_detoriation_window")

required = [
    "Data/raw", "Data/interim", "Data/processed", "Data/metadata",
    "Documentation", "Notebooks", "Scripts", "Results/figures",
    "Results/tables", "Results/model_weights", "Results/reports",
]
missing = [p for p in required if not (ROOT / p).exists()]
if missing:
    print("❌ Missing:", missing)
else:
    print("✅ All folders present")
```

---

## STAGE 3 — Data Files ✅

| File | Location | Format | Size |
|------|----------|--------|------|
| `instability_panel` | `Data/processed/` | `.parquet` | ~240 MB |
| `diagnosis_history` | `Data/processed/` | `.parquet` | ~4 MB |
| `inpatient_events`  | `Data/processed/` | `.parquet` | ~0.3 MB |
| `medication_event`  | `Data/processed/` | `.parquet` | ~4.5 MB |
| `outpatient_event`  | `Data/processed/` | `.parquet` | ~4.5 MB |
| `patient_summary`   | `Data/processed/` | `.parquet` | ~1.5 MB |

```python
# Verify all data files exist
import sys
sys.path.insert(0, str(ROOT))
from Scripts.data_converter import PATHS

for name, path in PATHS.items():
    status = f"✅ {path.stat().st_size/1024/1024:.1f} MB" if path.exists() else "❌ MISSING"
    print(f"  {name:25s} {status}")
```

> ⚠️ If any file is missing, run: `Scripts\convert_to_parquet.bat` from the project root.

---

## STAGE 4 — Standard Notebook Header ✅

Copy-paste this at the **top of every new notebook cell block**:

```python
# ── Notebook Header ────────────────────────────────────────────
import sys
import torch
import polars as pl
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from pathlib import Path

# Paths
ROOT      = Path(r"D:\source\repos\Psy_med_detoriation_window")
sys.path.insert(0, str(ROOT))
from Scripts.data_converter import PATHS, RESULTS

# Reproducibility
SEED = 42
np.random.seed(SEED)
torch.manual_seed(SEED)
DEVICE = "cuda" if torch.cuda.is_available() else "cpu"

# Quick check
print(f"✅ Device: {DEVICE} | Python: {sys.version.split()[0]} | Polars: {pl.__version__}")
```

---

## STAGE 5 — Per-Section Checklist ✅

> See [`Documentation/ml_best_practices_checklist.md`](./ml_best_practices_checklist.md) for the full copy-paste block.

Key rules — confirm before writing any model code:

- [ ] Loading from `Data/processed/*.parquet` — NOT raw CSV
- [ ] Scaler fitted on **train only**, applied to val/test
- [ ] `n_jobs=-1` on all Scikit-learn models
- [ ] PyTorch model pushed to `DEVICE`
- [ ] Figures saved to `Results/figures/`
- [ ] Weights saved to `Results/model_weights/`

---

## STAGE 6 — Git Hygiene ✅

Before ending a session:

```bash
git add -A
git commit -m "feat: <short description of what you did>"
git push origin main
```

> Never commit raw data files (`.csv`, `.parquet`). Add to `.gitignore` if not already there.

---

## Quick Reference

| Tool | When to use |
|------|-------------|
| **Polars** | Any file > ~50 MB |
| **Pandas** | Small files, compatibility |
| **DuckDB** (`Scripts/convert_to_parquet.bat`) | Adding new raw CSVs |
| **PyTorch** | Deep learning, GPU models |
| **XGBoost/LightGBM** | Tabular GPU baselines |
| **Scikit-learn** | CPU baselines, metrics |
