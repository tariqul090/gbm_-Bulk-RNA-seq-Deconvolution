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


Quickstart
Run the full pipeline locally (one command)
The repository bundles all required raw data (TCGA, CGGA, GLASS, Ivy GAP, signatures, and the pre-built single-cell reference), so the entire pipeline runs end-to-end from this folder.

# 1. Install R (>= 4.2) and Python (>= 3.10), then:

# 2. Restore the pinned R package library (into ./.Rlib; ~289 packages)
Rscript scripts/setup_env.R

# 3. Install the Python dependencies (GLASS cohort-build steps)
pip install -r requirements.txt

# 4. Run the whole pipeline (raw data -> publication figures)
Rscript run_all.R
#   or:  ./run_all.sh
run_all.R runs every step in dependency order and skips any step whose output already exists, so it is restartable and cheap to re-run. Useful modes:

Rscript run_all.R --smoke   # fast check: rebuild publication figures from
                            #   existing tables only (no heavy compute)
Rscript run_all.R --force   # re-run every step, ignoring existing outputs
A running log is written to results/tmp/run_all.log.

Explore the project

Review the science: docs/literature_review.md, docs/gap_analysis.md.
Review the data plan: docs/dataset_catalog.md and results/tables/main/gbm_rnaseq_datasets_VALIDATED.csv.
Review the methods: docs/analysis_blueprint.md.
Individual steps live in scripts/ and can be run standalone with Rscript scripts/<step>.R (each sources scripts/00_config.R for paths).
Cohorts

Discovery: TCGA-GBM (GDC STAR-Counts).
Validation: CGGA mRNAseq_693 and mRNAseq_325.
Longitudinal: GLASS (Synapse).
Spatial/context: Ivy GAP + selected GEO immunotherapy cohorts.
Methods at a glance
Deconvolution: BayesPrism + DWLS (optional MuSiC/Bisque) with a GBM-specific single-cell reference (malignant states + microglia/monocyte-derived macrophages/ MDSCs/DCs/lymphocytes). Cross-method concordance reported.
MES scoring: Neftel MES-like meta-module on the malignant compartment.
Survival: Kaplan-Meier + adjusted multivariable Cox (age, MGMT, IDH, treatment), Schoenfeld PH diagnostics, EPV ≥ 10, C-index with overfit caveat, BH-FDR.
Validation: direction-concordant replication in ≥ 1 independent cohort.
Reproducibility

R (>= 4.2) and Python (>= 3.10). R package versions are pinned exactly in renv.lock (289 packages, R 4.4.3, Bioconductor 3.20) and restored into the project-local .Rlib/ by scripts/setup_env.R; Python dependencies are pinned in requirements.txt. All file paths are resolved centrally by scripts/00_config.R (override the project root with the GBM_PROJ environment variable).

Bundled data. All required inputs are committed under data/ so the pipeline runs offline:

data/raw/GLASS/ — GLASS TPM matrix + clinical (requires free Synapse registration to re-download; bundled here so no login is needed).
data/raw/IvyGAP/ — Ivy GAP FPKM + region annotations.
data/raw/signatures/ — Neftel meta-modules, Verhaak centroids, gene sets.
data/preprocessed/reference/gbm_sc_reference.rds — the GBmap Core single-cell reference (22,719 genes x 4,218 cells, 16 cell types, 95 donors; Ruiz-Moreno et al., Nat Commun 2023). scripts/03_build_reference.R validates this file; an optional rebuild from the full 8 GB atlas is available via GBM_REBUILD_REFERENCE=1 (see that script's header).
TCGA (GDC) and CGGA are re-downloadable anonymously by scripts/01_download_data.R; their raw matrices are also bundled so the download step is skipped on re-run. The Pombo Antunes TAM-BDM marker set is a curated gene list (used in scripts/13_tam_bdm_markers.R), not a raw matrix, so no large Pombo files are bundled.
