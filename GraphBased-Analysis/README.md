# GraphBased-Analysis

## MORPHEUS

**Paper:** *MORPHEUS: A Graph-Based Multimodal Framework for Interpretable Subtyping of Non-Small Cell Lung Cancer*  
**Authors:** Gloria Riechi, Robert Eshun — NC A&T State University

### Overview

**MORPHEUS** (Multimodal Omics and Radiology-Pathology Hierarchical Encoding for NSCLC Unified Subtyping) is a graph neural network framework that jointly models RNA-Seq transcriptomic profiles and whole slide image (WSI) tissue architecture for interpretable LUAD/LUSC classification.

Unlike approaches that concatenate features or apply fixed-weight fusion, MORPHEUS assigns **patient-specific modality weights** via a learnable cross-modal attention mechanism, enabling both strong predictive performance and case-level interpretability.

### Architecture

MORPHEUS operates in four stages:

1. **Data acquisition & preprocessing** — RNA-Seq (FPKM-UQ) and WSI patch embeddings (UNI ViT-L/16)
2. **Graph construction** — two modality-specific graphs per patient:
   - *Gene Co-expression Graph (GCG):* genes as nodes, edges from Pearson correlation (top-K=20, |r| ≥ 0.3), fixed topology across patients
   - *WSI Spatial Graph (WSG):* tissue patches as nodes, edges from spatial k-NN (k=8) on 2D patch coordinates with edge features [distance, cosine similarity]
3. **Parallel GAT encoding:**
   - GCG encoder: 3 GAT layers (256→128→64), H=4 heads, global mean pooling → 64-dim embedding
   - WSG encoder: 3 GAT layers (512→256→128) with two DiffPool blocks (N→500→125 clusters) and gated attention pooling → 128-dim embedding
4. **Cross-modal attention fusion:** both embeddings projected to a shared 256-dim space; patient-specific weights (α_omics, α_wsi) computed via softmax scoring → fused representation → 2-layer FC classifier

### Interpretability

The per-patient attention weights α_omics and α_wsi indicate which modality drives each classification decision. This supports both patient-level inspection and cohort-level analysis of modality informativeness across subtypes.

### Training

- Optimizer: AdamW (lr = 1e-4, weight decay = 1e-5)
- Scheduler: Cosine Annealing with Warm Restarts (T0=10)
- Gradient accumulation over 4 steps; gradient clipping (ℓ2 norm ≤ 1.0)
- Early stopping: 15 epochs without validation AUC improvement (max 100 epochs)
- Evaluated over N=5 independent random seeds

### Cohort

| Split | Patients |
|-------|----------|
| Train | 577 |
| Validation | 83 |
| Test | 164 |
| **Total** | **825** (455 LUAD, 370 LUSC) |

### Baselines

| Model | Description |
|---|---|
| GCG-only | GCG encoder + classifier, no WSI |
| WSG-only | WSG encoder + classifier, no RNA-Seq |
| Early Fusion | Concatenated raw features → classifier |
| Late Fusion | Independent unimodal classifiers, averaged outputs |

---

## Dependencies

- Python 3.x
- `torch`, `torch_geometric`
- `numpy`, `pandas`, `scikit-learn`
- `h5py`
- `timm`, `huggingface_hub` (UNI model)
- `matplotlib`
