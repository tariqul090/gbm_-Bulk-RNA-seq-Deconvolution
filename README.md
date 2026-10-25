# gbm_-Bulk-RNA-seq-Deconvolution
Learning bulk RNA-seq data analysis to estimate the proportions of different cell types within glioblastoma (GBM) tumor samples. Exploring bioinformatics tools and computational methods to identify tumor microenvironment components, characterize immune cell populations, and understand cellular heterogeneity using gene expression data.

Directory layout
gbm-bulk-deconvolution/
├── data/
│   ├── raw/                # downloaded matrices + clinical (per cohort)
│   └── preprocessed/       # harmonized TPM/counts + reference + QC
├── scripts/                # numbered pipeline scripts (see scripts/README.md)
├── results/
│   ├── figures/
│   │   ├── main/           # main-text figures
│   │   └── supplement/     # supplementary figures
│   └── tables/
│       ├── main/           # main-text tables (incl. dataset catalog)
│       └── supplement/     # supplementary tables (incl. evidence table)
├── docs/                   # per-step documentation + review + blueprint
│   ├── literature_review.md
│   ├── gap_analysis.md
│   ├── dataset_catalog.md
│   ├── analysis_blueprint.md
│   └── 01_download.md ... 08_figures_tables.md
├── README.md               # this file
└── environment.yml         # reproducibility (R package versions)
