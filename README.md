# Connectome-Based Predictive Modelling of Cognitive Performance in Psychiatric Disorders

**Author:** Leong Rui Ying Clarisse | NTU Singapore | HP4210 BrainHack School 2026  
**GitHub:** [clarisseleong/UCLA-CPM-cognitive-psychiatry](https://github.com/clarisseleong/UCLA-CPM-cognitive-psychiatry)

---

## Overview

Can a single resting-state brain scan predict how well someone performs on cognitive tasks — across schizophrenia, bipolar disorder, ADHD, and healthy controls?

This project applies **Connectome-Based Predictive Modelling (CPM)** (Shen et al., 2017) to test whether resting-state functional connectivity (rsFC) can transdiagnostically predict individual cognitive performance across five domains: working memory, attention, verbal memory, processing speed, and inhibitory control.

The key idea: schizophrenia, bipolar disorder, and ADHD share overlapping cognitive deficits. If these arise from common disruptions in functional brain organisation, then a single FC-based model — trained without any diagnostic labels — should predict cognitive performance across all four groups. This is the **transdiagnostic hypothesis**.

---

## Background

Psychiatric disorders like schizophrenia, bipolar disorder, and ADHD differ in severity of cognitive impairment rather than which domains are affected (Vöhringer et al., 2013). Despite this, no neuroimaging biomarkers have been adopted in clinical psychiatric practice (Abi-Dargham et al., 2023). Individual resting-state connectomes are stable, unique, and predictive of behaviour (Finn et al., 2015) — making CPM a promising approach to bridge this gap.

---

## Data

### Resting-state fMRI timeseries
Bryant et al. (2023). Preprocessed rsfMRI timeseries from the UCLA Consortium for Neuropsychiatric Phenomics (CNP) LA5c Study.  
**Download:** https://doi.org/10.5281/zenodo.10431855 (CC-BY 4.0)  
Place the file at `data/UCLA_CNP_fMRI_TS.mat` before running.

**Preprocessing pipeline:**
- fMRIPrep v1.1.1 (motion correction, slice timing, normalisation)
- ICA-AROMA non-aggressive (automatic motion artefact removal)
- 2P + global mean regression (WM + CSF + global signal denoising)
- Timeseries extracted from 82 regions: 68 cortical (Desikan-Killiany atlas) + 14 subcortical
- 152 timepoints per subject

### Behavioural and demographic data
Poldrack et al. (2016). UCLA CNP LA5c Study. OpenNeuro ds000030.  
**Included in this repository** under `data/phenotype/`.

| Cognitive Domain   | Task                        | Variable        | Scoring          |
|--------------------|-----------------------------|-----------------|------------------|
| Working memory     | WAIS Letter-Number Seq.     | `lns_totalraw`  | Higher = better  |
| Attention          | CPT False Alarms            | `cpt_fa`        | Lower = better   |
| Verbal memory      | CVLT Total Correct          | `cvlt_totcor`   | Higher = better  |
| Processing speed   | Color Trails Time 1         | `crt_time1`     | Lower = better   |
| Inhibitory control | Stop Signal (SSRT)          | `sst_ses_ssrt`  | Lower = better   |

**Sample:** N = 260 matched across fMRI and behavioural data  
ADHD = 40, Bipolar = 49, Controls = 121, Schizophrenia = 50

---

## Methods

### Functional Connectivity Computation
For each subject, Pearson correlation was computed between every pair of 82 regional timeseries → 82×82 FC matrix. Fisher-Z transform (`atanh`) was applied to normalise r-values for statistical modelling. The upper triangle was flattened into a 3,321-edge feature vector per subject.

### Covariate Correction
Cognitive scores were residualised against age and gender using linear regression before CPM. This removes demographic confounds — the diagnostic groups differ systematically in age and gender composition. Working memory and attention showed spuriously significant predictions before correction (r ≈ −0.19, p = 0.003) that disappeared after, confirming they were demographic artefacts.

### CPM with Leave-One-Out Cross-Validation
1. Hold out one participant (test set)
2. Correlate each of 3,321 FC edges with cognitive score across N−1 training participants
3. Select edges at p < 0.01 → positive network + negative network
4. Net strength = Σ(positive edges) − Σ(negative edges)
5. Fit linear model: score ~ net_strength (training only)
6. Predict held-out participant's score
7. Repeat for all N; accuracy = Pearson r(predicted, observed)

**Why p < 0.01?** Standard threshold from Shen et al. (2017), balancing sensitivity vs overfitting.  
**Why LOO-CV?** Maximises training data at each fold; standard for CPM at N ≈ 260.

---

## Results

| Cognitive Domain   | N   | r     | p     |            |
|--------------------|-----|-------|-------|------------|
| Working Memory     | 260 | 0.027 | 0.668 |            |
| Attention          | 260 | 0.076 | 0.228 |            |
| Verbal Memory      | 260 | 0.149 | 0.017 | ✓ p < .05  |
| Processing Speed   | 260 | 0.056 | 0.377 |            |
| Inhibitory Control | 258 | 0.139 | 0.028 | ✓ p < .05  |

*After covariate correction for age and gender.*

### What this means

The main finding is that a single resting-state connectome model, with no knowledge of diagnosis, can significantly predict verbal memory and inhibitory control across schizophrenia, bipolar disorder, ADHD, and healthy controls. This supports the transdiagnostic hypothesis: these cognitive deficits share a common neural substrate in functional brain organisation, not just a shared diagnostic label.

**Verbal memory (r = 0.149, p = 0.017)**

rsFC significantly predicts CVLT (California Verbal Learning Test) scores across all four groups. The CVLT measures the ability to learn and recall a word list across multiple trials, which is a core measure of episodic verbal memory. The fact that a single FC pattern predicts this across SZ, BD, ADHD, and controls suggests there is a common disruption in hippocampal-cortical connectivity underlying verbal recall deficits transdiagnostically, consistent with posterior hippocampus and default mode network alterations linked to episodic memory in psychiatric populations (Dugre et al., 2021).

**Inhibitory control (r = 0.139, p = 0.028)**

rsFC significantly predicts stop-signal reaction time (SSRT) across all four groups. SSRT measures how quickly a person can stop a prepotent motor response, so higher SSRT means worse inhibitory control. The positive r here means the model correctly predicts worse inhibition for participants who actually performed worse, which is the expected direction. This is consistent with reduced right inferior frontal gyrus functional connectivity in SZ and BD during stop-signal tasks (Tsujii et al., 2018), and converges with and extends Lv et al. (2025) to include ADHD.

**Why only 2 out of 5 domains?**

This is itself an important finding. Working memory and attention were significant before covariate correction (r approx. -0.19, p = 0.003 each) but lost significance after removing variance explained by age and gender. This means their apparent FC-cognition link was a demographic confound: the diagnostic groups differ in age and gender composition, and without correction, CPM was picking up on those differences rather than genuine FC-cognition signal. This shows why covariate correction is non-negotiable in transdiagnostic designs. Processing speed was non-significant before and after correction, suggesting resting-state FC may not be the right tool for capturing its neural substrates; task-evoked FC during a speed task would be more appropriate.

**What this means clinically**

The domain-specificity of these results means a single resting-state scan cannot replace a full neuropsychological battery. However, the significant predictions for verbal memory and inhibitory control suggest rsFC could potentially be used to flag at-risk individuals in these specific domains, which is particularly relevant for early intervention in psychiatric populations where full neuropsychological assessments are time- and resource-intensive.

**Note on the bar chart**

Absolute r (|r|) is used to compare prediction magnitude across domains because some tasks are scored so that higher = worse (e.g. SSRT for inhibitory control, CPT false alarms for attention), which makes the sign of r dependent on task scoring rather than biology. All signed r values and p-values are in the table above.

---

## Repository Structure

```
├── README.md
├── UCLA_CPM_analysis.Rmd          # Full reproducible analysis script
├── UCLA_CPM_analysis.html         # Rendered output — all results and figures
├── CPM_summary.csv                # CPM prediction accuracy per cognitive domain
└── data/
    ├── UCLA_CNP_fMRI_TS.mat       # Download from Zenodo (link above) — not included (1.6 GB)
    └── phenotype/                 # Included in this repo
        ├── participants.tsv
        ├── wais.tsv
        ├── cpt.tsv
        ├── cvlt.tsv
        ├── colortrails.tsv
        └── stopsignal.tsv
```

---

## How to Reproduce

```r
# 1. Install required packages
install.packages(c("tidyverse", "R.matlab", "corrplot", "ggplot2",
                   "patchwork", "RColorBrewer", "scales", "psych"))

# 2. Download UCLA_CNP_fMRI_TS.mat from https://doi.org/10.5281/zenodo.10431855
#    Place at: data/UCLA_CNP_fMRI_TS.mat

# 3. Open UCLA_CPM_analysis.Rmd in RStudio
#    Set working directory to repo root
#    Knit — CPM takes ~10-15 minutes
```

The `.html` output is included so you can view all results and figures without running the code.

---

## Limitations & Future Directions

- **Sample size:** ADHD n = 40 limits group-specific conclusions. Larger balanced samples needed.
- **Atlas resolution:** 82 regions (Desikan-Killiany) — finer parcellations (e.g. Schaefer-400) may improve prediction.
- **Permutation testing:** p-values from Pearson r should be validated with permutation-based null distributions.
- **Network specificity:** Future work should identify which specific brain networks (DMN, frontoparietal, etc.) drive the verbal memory and inhibitory control predictions.
- **Missing covariates:** Medication status and years of education are absent from the public UCLA CNP release but are meaningful confounds.
- **Processing speed:** Task-evoked rather than resting-state FC may be more appropriate for this domain.

---

## References

- Abi-Dargham, A., et al. (2023). Candidate biomarkers in psychiatric disorders: state of the field. *World Psychiatry*, 22(2), 236–262. https://doi.org/10.1002/wps.21078
- Bryant, A. G., et al. (2023). Data files to support "Extracting interpretable signatures of whole-brain dynamics through systematic comparison". *Zenodo*. https://doi.org/10.5281/zenodo.10431855
- Esteban, O., et al. (2019). fMRIPrep: a robust preprocessing pipeline for functional MRI. *Nature Methods*, 16, 111–116. https://doi.org/10.1038/s41592-018-0235-4
- Finn, E. S., et al. (2015). Functional connectome fingerprinting: identifying individuals using patterns of brain connectivity. *Nature Neuroscience*, 18(11), 1664–1671. https://doi.org/10.1038/nn.4135
- Lv, Q., et al. (2025). Transdiagnostic Connectome-Based Prediction of Response Inhibition. *Human Brain Mapping*, 46(3). https://doi.org/10.1002/hbm.70158
- Poldrack, R. A., et al. (2016). A phenome-wide examination of neural and cognitive function. *Scientific Data*, 3, 160110. https://doi.org/10.1038/sdata.2016.110
- Shen, X., et al. (2017). Using connectome-based predictive modeling to predict individual behavior from brain connectivity. *Nature Protocols*, 12, 506–518. https://doi.org/10.1038/nprot.2016.178
- Vöhringer, P. A., et al. (2013). Cognitive impairment in bipolar disorder and schizophrenia. *Journal of Psychiatric Research*, 47, 166–172.
