---
title: "Self-Supervised Representation Learning with SimCLR"
date: 2026-03-18
draft: false
tags: [self-supervised-learning, contrastive-learning, computer-vision]
summary: "Implementing SimCLR from scratch to learn visual representations without labels using contrastive learning on CIFAR-10."
---

## Goal

Learn visual representations without labels using contrastive learning. Specifically, implement SimCLR (Simple Framework for Contrastive Learning of Visual Representations) from scratch and evaluate the quality of learned embeddings on CIFAR-10.

## Approach

**Architecture**: ResNet-18 encoder + 2-layer MLP projection head (128-dim output).

**Data augmentation pipeline** (critical for SimCLR):
- Random resized crop
- Color jitter (brightness, contrast, saturation, hue)
- Random horizontal flip
- Random grayscale conversion

**Training**:
- NT-Xent loss (normalized temperature-scaled cross-entropy)
- Temperature `τ = 0.5`
- Batch size: 512 (1024 pairs)
- LARS optimizer, lr=0.3, weight decay=1e-6
- 500 epochs pretraining

**Evaluation**: Linear probe on frozen encoder features (linear classifier trained on top of fixed representations).

```python
# Core NT-Xent loss
def nt_xent_loss(z_i, z_j, temperature=0.5):
    z = torch.cat([z_i, z_j], dim=0)
    sim = F.cosine_similarity(z.unsqueeze(1), z.unsqueeze(0), dim=2)
    sim = sim / temperature

    # Mask out self-similarity
    mask = torch.eye(2 * N, dtype=torch.bool, device=sim.device)
    sim.masked_fill_(mask, -9e15)

    # Positive pairs
    pos = torch.cat([
        torch.diag(sim, N),
        torch.diag(sim, -N)
    ])

    loss = -pos + torch.logsumexp(sim, dim=1)
    return loss.mean()
```

## Results

| Method | CIFAR-10 Linear Probe Accuracy |
|--------|-------------------------------|
| Random init | 39.2% |
| SimCLR (100 epochs) | 78.4% |
| SimCLR (500 epochs) | 88.1% |
| Supervised baseline | 93.6% |

**Key observations**:
- Augmentation composition matters more than any single augmentation
- Removing color jitter dropped accuracy by ~8%
- Larger batch sizes consistently helped (more negatives)
- Projection head is essential during training but discarded at eval

## Failures & Learnings

**What didn't work**:
- Small batch sizes (< 256) degraded performance significantly due to fewer negative pairs
- Standard SGD underperformed LARS for large-batch training
- Using too aggressive augmentations (e.g., extreme crops) hurt early training

**Key learnings**:
1. The projection head learns to discard information not useful for the contrastive task -- the representation *before* projection generalizes better
2. Temperature is surprisingly important: too low collapses representations, too high makes the task too easy
3. Training longer consistently helps -- unlike supervised learning, SSL doesn't overfit as easily on augmentation-heavy regimes

## References

- [SimCLR Paper (Chen et al., 2020)](https://arxiv.org/abs/2002.05709)
- [LARS Optimizer (You et al., 2017)](https://arxiv.org/abs/1708.03888)
