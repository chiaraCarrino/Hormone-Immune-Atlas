# HormImmune Atlas
### Can hormonal signaling predict immune response in tumors?
> A pan-cancer analysis across 12 solid tumor types · TCGA · ssGSEA · CIBERSORTx · Random Forest

---

## The Question

Immunotherapy works by reactivating the immune system against cancer — but it only helps a fraction of patients. A key reason is the **immune phenotype** of the tumor:

| Phenotype | What it means | Immunotherapy response |
|---|---|---|
| 🔴 Inflamed | Immune cells infiltrate the tumor core | Likely to respond |
| 🟡 Excluded | Immune cells present but blocked at the border | Variable |
| ⚫ Desert | No immune infiltration | Unlikely to respond |

**What drives these phenotypes is poorly understood.** Hormones circulate systemically and act simultaneously on tumor cells and immune cells — yet their role in shaping the tumor immune microenvironment (TIME) has been largely overlooked outside classically hormone-dependent cancers (breast, prostate).

**This project asks:** do hormonal pathway signatures systematically predict immune phenotype — and can they serve as biomarkers for immunotherapy stratification?

---

## Data

| Source | Content | Scale |
|---|---|---|
| [TCGA](https://www.cancer.gov/tcga) | RNA-seq expression (TPM), matched tumor + adjacent normal | ~9,000 samples · 12 tumor types |
| [MSigDB](https://www.gsea-msigdb.org/) | Curated hormonal gene sets (Hallmark, C2, C3, C5, C6) | 7 signatures |
| CIBERSORTx | Immune cell type deconvolution from bulk RNA-seq | 22 immune cell fractions |

---

## Pipeline Overview

<img src="https://github.com/chiaraCarrino/Hormone-Immune-Atlas/blob/main/images/work-in-progress-icon.jpg" alt="WORK IN PROGRESS! MORE NOTEBOOK WILL BE ADDED TO THE REPOSITORY SOON" style="width:50%; height:auto;">


```
RNA-seq (TCGA)
    │
    ├─ 01_eda_and_scoring                                      → Quality control, batch effects, expression distributions
    │
    ├─ 02_ssgsea_calculation_AND_immune_deconvolution          → Score each sample for hormonal pathway activity
    │
    ├─ 03_hormone_immune_correlation                           → Spearman correlation: hormonal scores × immune cell fractions
    │                                                           (per tumor type, tumor vs. normal separately)
    │
    ├─ 04_predictive_modelling                                 → PCA on correlation matrices to identify cross-tumor patterns
    │
    └─ 05_immune_phenotype_classifier                          → Random Forest: hormonal scores → immune phenotype label
                                                                 Evaluation: accuracy, ROC-AUC, confusion matrix
                                                                    Interpretation: SHAP values per tumor type
```

---

## Key Outputs

- **Correlation matrices** — per tumor type, per tissue (TUM / NAT): `results/correlation_matrices/`
- **Immune phenotype labels** — rule-based assignment from deconvolution: `results/immune_phenotype_labels.csv`
- **Classifier performance** — per-tumor accuracy + ROC curves: `results/phenotype_classifier_performance.csv`
- **SHAP rankings** — top hormonal predictors per tumor type


## Methods at a Glance

- **ssGSEA** — single-sample Gene Set Enrichment Analysis; scores each sample independently for hormonal pathway activity without needing a reference group
- **Spearman correlation** — non-parametric; robust to non-normal expression distributions and outliers; zeros in deconvolution output treated as missing (not true absence)
- **CIBERSORTx** — reference-based deconvolution estimating proportions of 22 immune cell types from bulk RNA-seq
- **Random Forest + SHAP** — classification of immune phenotype from hormonal scores; SHAP provides per-feature, per-tumor interpretability

---

## References

1. TCGA Research Network. *Nature* (2013). [cancer.gov/tcga](https://www.cancer.gov/tcga)
2. Barbie DA et al. *Nature* 462, 108–112 (2009). — ssGSEA. [doi:10.1038/nature08460](https://doi.org/10.1038/nature08460)
3. Liberzon A et al. *Cell Systems* 1, 417–425 (2015). — MSigDB. [doi:10.1016/j.cels.2015.12.004](https://doi.org/10.1016/j.cels.2015.12.004)
4. Newman AM et al. *Nature Biotechnology* 37, 773–782 (2019). — CIBERSORTx. [doi:10.1038/s41587-019-0114-2](https://doi.org/10.1038/s41587-019-0114-2)
