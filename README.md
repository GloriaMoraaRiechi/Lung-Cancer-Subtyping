# Multimodal Cancer Data Analysis — TCGA LUAD/LUSC

This repository contains three interconnected research projects investigating multimodal machine learning approaches for non-small cell lung cancer (NSCLC) subtype classification (LUAD vs. LUSC) using data from The Cancer Genome Atlas (TCGA).

**Authors:** Gloria Riechi, Robert Eshun, Kristen Rhinehardt  
**Affiliation:** Department of Computational Data Science and Engineering, North Carolina A&T State University

---

## Repository Structure

```
├── Data/                        # Data preparation and preprocessing pipelines
├── Federated-Learning/          # Federated learning framework for multimodal cancer data
├── GraphBased-Analysis/         # MORPHEUS: Graph-based multimodal classification framework
```

---

## Projects Overview

### 📁 Data
Preprocessing and preparation pipelines for four biological modalities sourced from TCGA-LUAD and TCGA-LUSC cohorts: RNA-Seq expression profiles, DNA methylation, whole slide images (WSI), and clinical data. The final harmonized cohort covers 825 patients with complete data across all modalities.

→ See [`Data/README.md`](Data/README.md)

---

### 📁 Federated-Learning
A privacy-preserving federated learning framework that trains models on distributed multimodal cancer data without sharing raw patient records. Simulates a client-server FL environment with FedAvg and FedProx aggregation strategies, evaluated across IID and non-IID data distributions and varying numbers of clients (K = 2, 5, 10).

→ See [`Federated-Learning/README.md`](Federated-Learning/README.md)

---

### 📁 GraphBased-Analysis
**MORPHEUS** — a graph neural network framework that jointly models RNA-Seq transcriptomic profiles and WSI tissue architecture using dual Graph Attention Networks (GATs). Features a learnable cross-modal attention fusion mechanism that assigns patient-specific modality weights for interpretable classification. Also includes a systematic comparison of early fusion and late fusion strategies across all four modalities.

→ See [`GraphBased-Analysis/README.md`](GraphBased-Analysis/README.md)

---

## Dataset

All experiments use publicly available data from [The Cancer Genome Atlas (TCGA)](https://www.cancer.gov/tcga), specifically the TCGA-LUAD and TCGA-LUSC cohorts, accessed via the NIH Genomic Data Commons (GDC) portal. No new human subjects research was conducted. Data access follows TCGA data use policies.

| Modality         | Feature Dim | Patients |
|------------------|-------------|----------|
| RNA-Seq          | 5,000       | 825      |
| DNA Methylation  | 10,000      | 825      |
| WSI Features     | 1,024       | 825      |
| Clinical Data    | 15          | 825      |

**Task:** Binary classification — Lung Adenocarcinoma (LUAD) vs. Lung Squamous Cell Carcinoma (LUSC)

---

## Citation

