# UCLA-CPM-cognitive-psychiatry

# Connectome-Based Predictive Modelling of Cognitive Performance in Psychiatric Disorders

**Author:** Leong Rui Ying Clarisse | NTU Singapore | HP4210 BrainHack School 2026

## Project Overview

This project investigates whether resting-state functional connectivity can predict individual cognitive performance transdiagnostically across schizophrenia, bipolar disorder, ADHD, and healthy controls.

Using the UCLA Consortium for Neuropsychiatric Phenomics dataset (N = 247), I applied Connectome-Based Predictive Modelling (CPM) with leave-one-out cross-validation across five cognitive domains: working memory, attention, verbal memory, processing speed, and inhibitory control. Cognitive scores were residualised against age and gender prior to CPM.

## Key Findings

| Cognitive Domain | r | p |
|---|---|---|
| Working Memory | -0.050 | 0.431 |
| Attention | -0.031 | 0.627 |
| Verbal Memory | -0.147 | 0.021 * |
| Processing Speed | -0.027 | 0.671 |
| Inhibitory Control | 0.158 | 0.013 * |

*p < .05

Resting-state FC significantly predicted verbal memory and inhibitory control after controlling for age and gender.

## Dataset

UCLA Consortium for Neuropsychiatric Phenomics (Poldrack et al., 2016). Publicly available on OpenNeuro: https://openneuro.org/datasets/ds000030

Raw data files are not included in this repository. Download from OpenNeuro and place in your working directory before running.

## Repository Contents

- `UCLA_CPM_analysis.Rmd` — full analysis script
- `UCLA_CPM_analysis.html` — knitted output with all results and figures
- `CPM_summary.csv` — CPM prediction accuracy per cognitive domain
- `graph_metrics_per_participant.csv` — graph theory metrics per participant

## How to Run

1. Download the UCLA CNP dataset from OpenNeuro
2. Update file paths in Section 2 of the Rmd to your local directory
3. Knit `UCLA_CPM_analysis.Rmd` in RStudio (takes ~30-40 mins)

## Dependencies

R packages: tidyverse, ggplot2, corrplot, RColorBrewer, viridis, igraph, reshape2, patchwork, ggraph, tidygraph, scales, psych

## References

- Abi-Dargham et al. (2023). Candidate biomarkers in psychiatric disorders: state of the field. *World Psychiatry*. https://doi.org/10.1002/wps.21078
- Arias et al. (2022). Quantifying the Global Burden of Mental Disorders and Their Economic Value. *EClinicalMedicine*. https://doi.org/10.1016/j.eclinm.2022.101675
- Barron et al. (2020). Transdiagnostic, Connectome-Based Prediction of Memory Constructs Across Psychiatric Disorders. *Cerebral Cortex*. https://doi.org/10.1093/cercor/bhaa371
- Esteban et al. (2019). fMRIPrep: a robust preprocessing pipeline for functional MRI. *Nature Methods*. https://doi.org/10.1038/s41592-018-0235-4
- Finn et al. (2015). Functional connectome fingerprinting: identifying individuals using patterns of brain connectivity. *Nature Neuroscience*. https://doi.org/10.1038/nn.4135
- Lv et al. (2025). Transdiagnostic Connectome-Based Prediction of Response Inhibition. *Human Brain Mapping*. https://doi.org/10.1002/hbm.70158
- Poldrack et al. (2016). A phenome-wide examination of neural and cognitive function. *Scientific Data*. https://doi.org/10.1038/sdata.2016.110
- Shen et al. (2017). Using connectome-based predictive modeling to predict individual behavior from brain connectivity. *Nature Protocols*. https://doi.org/10.1038/nprot.2016.178
- Zhu et al. (2021). Dynamic functional connectome predicts individual working memory performance across diagnostic categories. *NeuroImage: Clinical*. https://doi.org/10.1016/j.nicl.2021.102593
