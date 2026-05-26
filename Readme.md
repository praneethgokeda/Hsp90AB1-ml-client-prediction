# Machine Learning Prediction of Hsp90β-Dependent Mitochondrial Protein Interactions in Cardiac Ischemia-Reperfusion Injury

**Author:** Praneeth Gokeda  
**Data:** Lam Laboratory, University of Delaware  
**Course:** Applied Machine Learning (BINF 690), May 2025  
**Type:** Individual Project

---

## Overview

Heat shock protein 90 beta (Hsp90β) plays a paradoxical **pro-necrotic role** in cardiac ischemia-reperfusion (I/R) injury. Rather than being protective, Hsp90β stabilizes Cyclophilin D (CypD) and promotes mitochondrial permeability transition pore (mPTP) opening, driving cardiac cell death during reperfusion. Cardiac-specific Hsp90β ablation has been shown to reduce infarct size and offer cardioprotection (Lam Laboratory, University of Delaware).

This project applies **supervised machine learning** to multi-omics data from Hsp90β knockout experiments to predict which mitochondrial proteins are Hsp90β-dependent clients, and to identify the molecular features that best characterize Hsp90β client recognition.

---

## Biological Question

> Can machine learning predict which mitochondrial proteins depend on Hsp90β using transcriptomic knockout signatures?

---

## Key Results

| Model | ROC-AUC | Avg Precision |
|-------|---------|---------------|
| Random Forest | 0.654 ± 0.067 | 0.043 ± 0.046 |
| **SVM (Best)** | **0.740 ± 0.095** | **0.064 ± 0.051** |
| Neural Network | 0.456 ± 0.111 | 0.012 ± 0.004 |

- **Permutation test p = 0.0099** — results are statistically significant
- **Top feature: alphaVSbeta_logFC (32%)** — isoform specificity is the strongest predictor
- HAX-1 and CypD scored low (1.3% and 1.4%) — consistent with post-translational regulation
- All models evaluated using **nested cross-validation** (Varma & Simon, 2006)

---

## Methods Summary

### Data
Four datasets from the Lam Laboratory (permission obtained from PI):
1. **Human mitochondrial proteomics** — pharmacological Hsp90β modulation (46 confirmed clients → label=1, 4,038 non-clients → label=0)
2. **Mouse Beta KO RNA-Seq** — Hsp90β knockout vs wildtype cardiac transcriptomics (features: betaKO_logFC, betaKO_FDR, betaKO_logCPM)
3. **Alpha vs Beta KO comparison** — isoform specificity features (alphaVSbeta_logFC, alphaVSbeta_FDR)
4. **Human cardiac proteomics** — Non-Failing vs ICM hearts (disease validation only, not used in training)

### Feature Matrix
- 4,084 proteins × 5 transcriptomic features
- Class imbalance: 87:1 (46 positives vs 4,038 negatives)
- Features excluded to prevent data leakage: Increase with Val, Decrease with Gel, Molecular weight

### Models
- **Random Forest** — 300 trees, class_weight='balanced', Gini impurity feature importance
- **SVM** — RBF kernel, GridSearchCV for C and gamma, class_weight='balanced'
- **Neural Network** — MLP with StandardScaler pipeline, early stopping

### Evaluation
- Nested cross-validation: outer 5-fold + inner 3-fold
- Metrics: ROC-AUC, Average Precision, F1, MCC
- Permutation test (n=100) for statistical significance

---

## Repository Structure

```
hsp90b-ml-client-prediction/
│
├── praneeth_ML_final.ipynb          # Main analysis notebook
├── Final_Report_Praneeth_Gokeda_BINF690.docx  # Final written report
├── requirements.txt                  # Python dependencies
├── README.md                         # This file
│
├── data/
│   └── README_data.md               # Data description (files not included)
│
└── figures/
    ├── feature_importance_rf.png     # Random Forest feature importance
    ├── roc_curve_rf_final.png        # RF ROC curve
    ├── roc_curve_svm.png             # SVM ROC curve
    ├── roc_curve_all_models.png      # Combined ROC comparison
    └── disease_validation.png        # Disease validation figure
```

---

## How to Run

### Requirements
```bash
pip install -r requirements.txt
```

### Data
Data files are confidential lab data from the Lam Laboratory, University of Delaware.  
To request access for research purposes contact: **[gpraneet@udel.edu]**  
See `data/README_data.md` for complete data description and required file structure.

### Running the Notebook
```bash
jupyter notebook praneeth_ML_final.ipynb
```

Run cells sequentially. The notebook is organized into 10 sections:
1. Data loading and exploration
2. Feature engineering and dataset integration
3. Labeled dataset construction
4. Random Forest with nested CV and feature importance
5. SVM with nested CV
6. Neural Network MLP with nested CV
7. Combined ROC curve comparison
8. Permutation test for statistical significance
9. HAX-1 and CypD prediction
10. Disease validation

---

## Requirements

```
pandas>=2.0
numpy>=1.24
scikit-learn>=1.3
matplotlib>=3.7
scipy>=1.11
openpyxl>=3.1
jupyter>=1.0
```

---

## Key Biological Finding

The most novel result of this project is that **isoform specificity (alphaVSbeta_logFC) is the strongest predictor of Hsp90β client status (32% feature importance)**. This means true Hsp90β clients are those that cannot be rescued by compensatory Hsp90α upregulation when Hsp90β is knocked out — a computationally derived biological insight consistent with the Lam Laboratory's experimental findings on Hsp90β's unique role in cardiac I/R injury.

---

## Limitations

- Small positive training set (n=46) limits model performance
- Transcriptomic features cannot capture post-translational Hsp90β clients (HAX-1, CypD)
- Disease validation dataset underpowered (96 proteins, 6 patients/group)
- Neural network performance poor due to insufficient positive examples

---

## Future Work

- [ ] Add ESM-2 protein language model embeddings as sequence features
- [ ] Include proteomics-level features to capture post-translational regulation
- [ ] Expand prediction to full MitoCarta 3.0 mitochondrial proteome (1,136 proteins)
- [ ] Apply Graph Neural Network using STRING protein interaction network
- [ ] Validate top novel predictions experimentally in the Lam Laboratory

---

## References

1. Varma, S. & Simon, R. (2006). Bias in error estimation when using cross-validation for model selection. *BMC Bioinformatics*, 7(1), 91.
2. Lam, C.K. et al. (2015). HAX-1 regulates cyclophilin-D levels and mitochondria permeability transition pore in the heart. *PNAS*, 112(26), E6466–E6475.
3. Pedregosa, F. et al. (2011). Scikit-learn: Machine learning in Python. *JMLR*, 12, 2825–2830.

---

## Acknowledgments

Data provided by the Lam Laboratory, University of Delaware. All raw data files are excluded from this repository in accordance with laboratory data sharing policies.

---

*Bioinformatics M.S. Program | University of Delaware | 2025*
