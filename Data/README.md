# Data — Multimodal Preprocessing Pipelines

This folder contains the data preparation and preprocessing pipelines for the multimodal TCGA LUAD/LUSC cohort used across all three projects in this repository.

---

## Overview

Raw data was sourced from The Cancer Genome Atlas (TCGA) LUAD and LUSC cohorts via the NIH Genomic Data Commons (GDC) portal. Patients were included only if complete data was available across all four biological modalities. After harmonization, the final cohort comprised **825 patients** (456 LUAD, 369 LUSC), split into 660 training and 165 test samples using a stratified 80/20 split.

All patients are aligned by TCGA barcode identifiers to ensure consistent cross-modal partitioning.

---

## Modalities & Preprocessing

### RNA-Seq
- Raw FPKM (or STAR-Counts) values processed with log2 transformation
- Low-expression genes filtered (non-zero in ≥20% of samples, mean raw count ≥10)
- TMM normalization followed by log2(CPM + 1) transformation
- Top genes selected by variance (MAD-based); final dimensionality: ~5,000 genes
- Z-score normalization per gene across the training set

### DNA Methylation
- Beta values from Illumina Infinium HumanMethylation450 BeadChip
- CpG sites with >20% missing values excluded
- Top 10,000 most variable CpG sites selected by variance
- Missing values imputed using KNN (k=5)
- Beta values logit-transformed to M-values for variance stabilization
- Z-score normalized per probe

### Whole Slide Images (WSI)
- H&E-stained SVS slides downloaded from GDC
- Tissue segmentation via HSV-based color thresholding with morphological cleaning
- Up to 2,000 patches sampled per slide (224×224 px, 20× magnification)
- Quality filtering: Laplacian blur variance >80, mean intensity 20–235
- Patch embeddings extracted using the **UNI foundation model** (ViT-L/16, pretrained on 100M+ histopathology images)
- Each patch encoded into a 1,024-dimensional feature vector
- Stored in HDF5 format (`.h5`); slide-level representation obtained via mean pooling → 1,024-dim vector per patient

### Clinical Data
- Categorical features one-hot encoded
- Continuous features standardized to zero mean and unit variance
- Missing values imputed using median imputation
- Final dimensionality: 15 features

---

## Output Format

Preprocessed features are exported per modality and aligned by TCGA patient barcode. The processed data serves as input to both the `Federated-Learning` and `GraphBased-Analysis` modules.

---

## Key Script

| Script | Description |
|--------|-------------|
| `wsiDataPreparation.ipynb` | End-to-end WSI patch extraction, quality filtering, and UNI feature embedding pipeline |

---

## Dependencies

- Python 3.x
- `numpy`, `pandas`, `scikit-learn`
- `openslide-python` (WSI processing)
- `h5py` (HDF5 storage)
- `torch`, `timm` (UNI feature extraction)
- `huggingface_hub` (model loading: `MahmoodLab/UNI`)
- `edgeR` (R, for RNA-Seq TMM normalization)
