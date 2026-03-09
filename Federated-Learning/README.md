# Federated Learning — Privacy-Preserving Multimodal Cancer Analysis

This folder implements a privacy-preserving federated learning (FL) framework for distributed analysis of multimodal cancer data from TCGA, without sharing raw patient records across clients.

**Paper:** *Federated Learning for Distributed Multimodal Cancer Data Analysis: A Simulation Study Using TCGA*  
**Authors:** Gloria Riechi, Robert Eshun, Kristen Rhinehardt — NC A&T State University

---

## Overview

Federated learning enables collaborative model training across distributed data sources (simulating hospital silos) by exchanging only model parameters — never raw patient data. This framework evaluates FL performance across four biological modalities, two aggregation algorithms, and varying client counts on TCGA-LUAD and TCGA-LUSC data.

**Task:** Binary classification — LUAD vs. LUSC  
**Cohort:** 825 patients, 660 training / 165 test (stratified 80/20 split)

---

## Framework Architecture

A simulated client-server architecture is run on a single machine:

1. The server broadcasts the current global model to selected clients
2. Each client trains locally on its own data partition for E epochs
3. Clients send updated weights back to the server (no raw data shared)
4. The server aggregates weights via **FedAvg** (size-weighted averaging) to produce the updated global model

This loop repeats for T = 30 global communication rounds.

---

## Experimental Design

| Variable | Values |
|----------|--------|
| Modalities | RNA-Seq, DNA Methylation, WSI Features, Clinical Data |
| Clients (K) | 2, 5, 10 |
| Data distribution | IID, Non-IID (Dirichlet α = 0.5, α = 1.0) |
| Aggregation | FedAvg, FedProx |
| Rounds | T = 30 |
| Random seeds | 3 independent runs per configuration |

For K = 10, partial client participation (C = 0.6) is used per round.

---

## Model Architecture

A three-layer MLP is used as the local model across all clients and modalities:

```
Input (d) → Dense(128, ReLU, Dropout=0.2) → Dense(64, ReLU, Dropout=0.2) → Output(2, Softmax)
```

- Optimizer: Adam (lr = 1e-3, β1 = 0.9, β2 = 0.999, L2 decay = 1e-5)
- Loss: Cross-entropy
- Batch size: 32
- Implemented in TensorFlow 2.x

---

## Key Results

| Modality | Centralized AUC | Best Federated AUC | Gap |
|---|---|---|---|
| RNA-Seq | 0.9800 | 0.9784 (K=10, IID) | -0.16% |
| DNA Methylation | 0.9750 | 0.9785 (K=10, IID) | +0.36% |
| WSI Features | 0.8700 | 0.8741 (K=10, non-IID FedProx) | +0.47% |
| Clinical Data | 0.8150 | 0.8103 (K=5, IID) | -0.58% |

- Molecular modalities (RNA-Seq, DNA Methylation) converge in 4–8 rounds and are robust to non-IID data heterogeneity
- FedProx provides statistically significant improvements for weaker modalities (WSI: +0.72%, Clinical: +0.90%) under non-IID conditions
- The framework scales effectively to 10 clients without systematic performance degradation for molecular modalities

---

## Evaluation Metrics

- Classification Accuracy
- AUC-ROC (primary metric)
- Cross-Entropy Loss
- Learning curves (accuracy and loss vs. communication round)
- Statistical significance via Wilcoxon signed-rank test (p < 0.05)

---

## Reproducibility

All experiments use fixed random seeds across NumPy, TensorFlow, and Python's `random` module. Code, model configurations, and data split indices will be made available upon publication.

---

## Dependencies

- Python 3.x
- `tensorflow >= 2.20.0`
- `numpy`, `pandas`, `scikit-learn`
- `scipy` (Wilcoxon tests)
- `matplotlib` (learning curves)
