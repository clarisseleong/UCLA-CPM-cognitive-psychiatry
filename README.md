# Connectome-Based Predictive Modelling of Cognitive Performance in Psychiatric Disorders

**Author:** Leong Rui Ying Clarisse | NTU Singapore | HP4210 BrainHack School 2026

---

## Project Overview

This project investigates whether resting-state functional connectivity (rsFC) can **transdiagnostically** predict individual cognitive performance across schizophrenia (SZ), bipolar disorder (BD), ADHD, and healthy controls using **Connectome-Based Predictive Modelling (CPM)** (Shen et al., 2017).

The core question: if cognitive deficits in SZ, BD, and ADHD share a common neural substrate, then a single rsFC-based model should be able to predict cognitive performance across all four groups — without being trained on any diagnostic labels.

CPM was applied with leave-one-out cross-validation (LOO-CV) across five cognitive domains: working memory, attention, verbal memory, processing speed, and inhibitory control. Cognitive scores were residualised against age and gender before CPM to remove demographic confounds.

---

## Data Sources

**Resting-state fMRI timeseries:**
Bryant, A. G., Aquino, K., Parkes, L., Fornito, A., & Fulcher, B. D. (2023). Preprocessed rsfMRI timeseries from the UCLA Consortium for Neuropsychiatric Phenomics (CNP) LA5c Study (ds000030). *Zenodo*. https://doi.org/10.5281/zenodo.10431855

- Preprocessing: fMRIPrep v1.1.1 + ICA-AROMA (non-aggressive) + 2P + global mean regression
- Atlas: Desikan-Killiany (68 cortical + 14 subcortical = 82 regions)
- 152 timepoints per subject
- License: CC-BY 4.0

**Behavioural and demographic data:**
Poldrack, R. A., et al. (2016). UCLA CNP LA5c Study. *OpenNeuro* ds000030. https://doi.org/10.18112/openneuro.ds000030.v1.0.0

> Raw data files are **not** included in this repository. Download from the links above and place in `data/` before running.

---

## Key Findings

N = 260 (ADHD = 40, Bipolar = 49, Controls = 121, Schizophrenia = 50)

| Cognitive Domain   | r     | p     | Significant |
|--------------------|-------|-------|-------------|
| Working Memory     | 0.027 | 0.668 |             |
| Attention          | 0.076 | 0.228 |             |
| Verbal Memory      | 0.149 | 0.017 | ✓ p < .05   |
| Processing Speed   | 0.056 | 0.377 |             |
| Inhibitory Control | 0.139 | 0.028 | ✓ p < .05   |

*All results after covariate correction (age + gender).*

**Verbal memory** and **inhibitory control** are significantly predicted by resting-state FC transdiagnostically across all four groups.

Working memory and attention showed significant predictions before covariate correction (r ≈ −0.19, p = 0.003), but lost significance after — confirming these were driven by demographic differences between groups, not genuine FC-cognition signal. Processing speed was non-significant before and after correction.

The bar chart uses |r| (absolute r) to compare prediction magnitude across domains, since some cognitive measures are scored so that higher = worse (e.g. SSRT for inhibitory control, CPT false alarms for attention). Signed r values are in the table above.

---

## Preprocessing Pipeline

```
Raw fMRI (UCLA CNP ds000030)
        ↓
fMRIPrep v1.1.1
(motion correction, slice timing, normalisation)
        ↓
ICA-AROMA (non-aggressive)
(automatic motion artefact removal)
        ↓
2P + Global Mean Regression
(WM + CSF + global signal denoising)
        ↓
Timeseries extraction
(82 ROIs × 152 TRs, Desikan-Killiany atlas)
        ↓
Pearson correlation → Fisher-Z transform
(82 × 82 FC matrix per subject)
        ↓
Upper triangle flattened
(3,321 unique edges per subject → input to CPM)
```

---

## CPM Method

1. Hold out one participant (test)
2. Correlate each of 3,321 FC edges with cognitive score in training set (N−1)
3. Select edges at p < 0.01 → positive network + negative network
4. Net strength = Σ(positive edges) − Σ(negative edges)
5. Fit linear model: score ~ net_strength (training only)
6. Predict held-out participant's score
7. Repeat for all N; accuracy = Pearson r(predicted, observed)

---

## Repository Contents

```
├── UCLA_CPM_analysis.Rmd        # Full analysis script (knit to reproduce)
├── UCLA_CPM_analysis.html       # Rendered output with all results and figures
├── CPM_summary.csv              # CPM prediction accuracy per cognitive domain
├── data/                        # Place downloaded data here (not included)
│   ├── UCLA_CNP_fMRI_TS.mat     # From Zenodo (Bryant et al., 2023)
│   └── phenotype/               # From OpenNeuro ds000030
│       ├── participants.tsv
│       ├── wais.tsv
│       ├── cpt.tsv
│       ├── cvlt.tsv
│       ├── colortrails.tsv
│       └── stopsignal.tsv
└── README.md
```

---

## How to Reproduce

1. Download `UCLA_CNP_fMRI_TS.mat` from [Zenodo](https://doi.org/10.5281/zenodo.10431855)
2. Download phenotype TSVs from [OpenNeuro ds000030](https://openneuro.org/datasets/ds000030)
3. Place files in `data/` as shown above
4. Open `UCLA_CPM_analysis.Rmd` in RStudio and set working directory to the repo root
5. Knit — CPM takes ~10–15 minutes

**R packages required:**
```r
install.packages(c("tidyverse", "R.matlab", "corrplot", "ggplot2",
                   "patchwork", "RColorBrewer", "scales", "psych"))
```

---

## References

- Bryant, A. G., et al. (2023). Data files to support "Extracting interpretable signatures of whole-brain dynamics through systematic comparison". *Zenodo*. https://doi.org/10.5281/zenodo.10431855
- Esteban, O., et al. (2019). fMRIPrep: a robust preprocessing pipeline for functional MRI. *Nature Methods*, 16, 111–116. https://doi.org/10.1038/s41592-018-0235-4
- Lv, Q., et al. (2025). Transdiagnostic Connectome-Based Prediction of Response Inhibition. *Human Brain Mapping*, 46(3). https://doi.org/10.1002/hbm.70158
- Poldrack, R. A., et al. (2016). A phenome-wide examination of neural and cognitive function. *Scientific Data*, 3, 160110. https://doi.org/10.1038/sdata.2016.110
- Shen, X., et al. (2017). Using connectome-based predictive modeling to predict individual behavior from brain connectivity. *Nature Protocols*, 12, 506–518. https://doi.org/10.1038/nprot.2016.178
- Vöhringer, P. A., et al. (2013). Cognitive impairment in bipolar disorder and schizophrenia. *Journal of Psychiatric Research*, 47, 166–172.
