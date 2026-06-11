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

Poldrack et al. (2016). A phenome-wide examination of neural and cognitive function. *Scientific Data*.  
Shen et al. (2017). Using connectome-based predictive modeling to predict individual behavior from brain connectivity. *Nature Protocols*.
