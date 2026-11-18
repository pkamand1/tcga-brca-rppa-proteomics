# Protein and phosphoprotein signatures of breast cancer subtypes: an RPPA analysis of TCGA

A reproducible R analysis of reverse phase protein array (RPPA) data from The Cancer Genome Atlas breast cancer cohort (PanCancer Atlas), comparing protein and phosphoprotein abundance across the PAM50 molecular subtypes, testing how well protein data alone recover those subtypes, and screening antibodies for association with progression-free survival.

The rendered report, with code, figures, and interpretation, is in [`docs/analysis.html`](docs/analysis.html).

## Questions

1. Do the PAM50 subtypes, defined from gene expression, have distinct protein and phosphoprotein profiles, and do the receptors that define clinical subtyping behave as expected?
2. How well does unsupervised clustering of the protein data alone recover the PAM50 subtypes?
3. Which proteins are associated with progression-free survival after adjustment for age and stage?

## Results in brief

| | Result |
| --- | --- |
| Tumours with RPPA data and a PAM50 subtype | 797 (LumA, LumB, Her2, Basal, Normal-like) |
| Antibodies analyzed | 191 of 198 (7 removed for more than 20% missing values), including phosphoprotein antibodies |
| Antibodies differing among subtypes (limma moderated F, FDR below 5%) | 173 of 191 |
| Largest differences, basal-like against luminal A | ER-alpha, GATA3, AR, and INPP4B lower; cyclin B1, FoxM1, ASNS, and cyclin E1 higher |
| Positive controls | ER-alpha, PR, and GATA3 highest in the luminal subtypes; total and phosphorylated HER2 highest in HER2-enriched tumours |
| Agreement of protein clusters with PAM50 | adjusted Rand index 0.22; 80% of basal-like tumours fall in one cluster, while luminal A and B mix |
| Antibodies associated with progression-free survival (Cox, adjusted for age and stage) | none at FDR below 5% (102 events among 788 patients); top nominal: PRAS40 pT246, Raptor, DJ-1, p27 |

The subtypes have strongly distinct protein profiles, and the receptor proteins behave exactly as clinical pathology would predict, which supports both the data and the labels. Protein data alone separate basal-like tumours well but blur the luminal subtypes, which differ mainly in proliferation. The survival screen is honest about its power: with about a hundred events, no single antibody passes correction for multiple testing, and the nominal associations are reported as hypotheses for validation.

## Methods

The RPPA values are the TCGA PanCancer Atlas data, normalized across batches by the TCGA RPPA core (replicate-based normalization) and distributed through cBioPortal. Antibodies with more than 20% missing values are removed; remaining gaps are left missing for the linear models and median-imputed only for PCA and clustering. Subtype differences are tested with limma (a moderated F-test across five subtypes and contrasts against luminal A, with Benjamini-Hochberg correction). Unsupervised structure is examined with PCA and Ward hierarchical clustering of the 80 most variable antibodies, compared with PAM50 by adjusted Rand index. Each antibody, standardized, is tested for association with the progression-free interval, the endpoint recommended for breast cancer in the PanCancer Atlas clinical data resource, in a Cox model adjusted for age and AJCC stage.

## Repository layout

```
analysis.Rmd                     the analysis (R Markdown)
docs/analysis.html               the rendered report
data/data_rppa.txt               RPPA matrix (antibodies x tumours)
data/data_clinical_patient.txt   PAM50 subtype, age, stage, and survival
data/data_clinical_sample.txt    sample annotations
results/tables/                  subtype F-tests, basal versus luminal A, cluster agreement, per-antibody Cox results
results/figures/                 positive controls, volcano, heatmap, PCA, forest plot, Kaplan-Meier
environment.yml                  conda environment (R 4.4, limma, survival, mclust, ggplot2, rmarkdown)
```

## How to run

```
mamba env create -f environment.yml
mamba activate tcga-brca-rppa-proteomics
Rscript -e 'rmarkdown::render("analysis.Rmd", output_file = "docs/analysis.html")'
```

## Data source

The data files are the `brca_tcga_pan_can_atlas_2018` study from the [cBioPortal datahub](https://github.com/cBioPortal/datahub), the public repository behind cBioPortal for Cancer Genomics. TCGA data are publicly available under the NIH Genomic Data Commons data use policies for open-access data.

## References

Liu, J., Lichtenberg, T., Hoadley, K. A., et al. (2018). An integrated TCGA pan-cancer clinical data resource to drive high-quality survival outcome analytics. *Cell, 173*(2), 400–416.

Ritchie, M. E., Phipson, B., Wu, D., et al. (2015). limma powers differential expression analyses for RNA-sequencing and microarray studies. *Nucleic Acids Research, 43*(7), e47.

## Author

Patricia K. Kamanda. Released under the MIT License.
