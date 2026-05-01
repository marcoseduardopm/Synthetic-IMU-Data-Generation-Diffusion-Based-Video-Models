# Synthetic Video Dataset for Cross-Modal IMU Knowledge Distillation

[![License: CC BY 4.0](https://img.shields.io/badge/License-CC%20BY%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by/4.0/)
[![Paper](https://img.shields.io/badge/Paper-IEEE%20Access-blue)](https://ieeeaccess.ieee.org/) (under revision) 
[![Institution](https://img.shields.io/badge/Institution-UTFPR-green)](https://www.utfpr.edu.br/)

This repository contains the open-source synthetic video dataset generated for the paper:

> **"Cross-Modal Knowledge Distillation for Edge HAR via Zero-Shot Text-to-Video Diffusion Models"**
> Marcos Eduardo Pivaro Monteiro, Jamil de Araujo Farhat, Bogdan Tomoyuki Nassu, João Alberto Fabro
> (under revision) *IEEE Access*, 2025/2026 — Federal University of Technology – Paraná (UTFPR), Brazil

---

## Overview

Physical data scarcity is a persistent bottleneck in training robust Human Activity Recognition (HAR) models for resource-constrained edge devices. This dataset addresses that limitation by providing synthetically generated human kinematic video sequences produced by state-of-the-art text-to-video diffusion models, **fully in a zero-shot regime** — no labeled video data was required.

The videos were generated to serve as a **cross-modal Vision Teacher**: a fine-tuned spatio-temporal network extracts soft-label probability distributions from these clips, which are then distilled into lightweight 1D IMU student architectures (BiLSTM, 2D CNN, Lightweight CNN) via a blended KL-Divergence + Cross-Entropy loss. The framework yields up to **+16.09% Macro F1-Score improvement** over a purely physical baseline at 25% data scarcity.

---

## Dataset Structure

```
synthetic_dataset/
├── hunyuan_1_5_720/       # HunyuanVideo 1.5
├── ltxv2_720/             # LTX-Video 2.0
├── ltxv2_3_720/           # LTX-Video 2.3
└── wan2_2_720/            # Wan 2.2
```

Each subfolder contains videos organized by the 7-class kinematic taxonomy described below.

---

## Kinematic Taxonomy

All videos are labeled according to a unified **7-class dynamic taxonomy**, designed to simultaneously govern text-prompt engineering and the heterogeneous label mapping of physical HAR datasets (PAMAP2 and SisFall):

| ID | Class | Description |
|----|-------|-------------|
| 0 | **Running** | Continuous high-cadence bipedal locomotion |
| 1 | **Falling** | Uncontrolled total-body collapse (15 distinct fall topologies) |
| 2 | **Lifting** | Bending and vertical load manipulation |
| 3 | **Stairs** | Ascending and descending stair navigation |
| 4 | **Jumping** | Repetitive vertical impulse movements (rope jumping, jumps) |
| 5 | **Stumble** | Partial loss of balance with inertial recovery |
| 6 | **Soccer** | Lateral field locomotion and upper-body dynamic actions |

---

## Video Generation Pipeline

### Models Used

| Folder | Model | Version |
|--------|-------|---------|
| `hunyuan_1_5_720` | [HunyuanVideo](https://github.com/Tencent/HunyuanVideo) | 1.5 |
| `ltxv2_720` | [LTX-Video](https://github.com/Lightricks/LTX-Video) | 2.0 |
| `ltxv2_3_720` | [LTX-Video](https://github.com/Lightricks/LTX-Video) | 2.3 |
| `wan2_2_720` | [Wan](https://github.com/Wan-Video/Wan2.2) | 2.2 |

### Prompt Engineering

For each activity class, a base prompt was multiplied across **10 distinct environmental modifiers** (e.g., *"in an industrial hallway"*, *"on a grassy park field"*, *"in a hospital corridor"*) to maximize background diversity and prevent the Vision Teacher from overfitting to specific scene contexts. All prompts enforced full-body framing:

> *"Full body completely framed in the shot from head to toe, [activity description], [environment modifier], photorealistic, 720p."*

### Technical Specifications

| Parameter | Value |
|-----------|-------|
| Resolution | 1280 × 704 (720p) |
| Aspect Ratio | 9:16 (portrait) |
| Frame Rate | 24 fps |
| Orientation | Portrait (enforced for full kinematic chain capture) |

---

## Quality Assurance & Filtering

All raw generated videos were evaluated using the **[VBench](https://github.com/Vchitect/VBench) framework** before inclusion in this dataset. Videos failing to meet a strict kinematic fidelity threshold of **≥ 0.80** on any of the three following metrics were automatically quarantined and excluded:

| Metric | Description |
|--------|-------------|
| **Temporal Flickering** | Frame-to-frame pixel stability; prevents erratic artifacts from corrupting learned features |
| **Motion Smoothness** | Continuity of kinematic trajectories; jitter would manifest as false acceleration spikes |
| **Subject Consistency** | Structural integrity of the human figure; prevents limb morphing or disappearing |

### Quality Scores of Retained Videos (Mean ± Std Dev)

| Model | Temporal Flickering | Motion Smoothness | Subject Consistency |
|-------|--------------------|--------------------|---------------------|
| LTX-Video 2.3 | 0.9855 ± 0.0180 | 0.9931 ± 0.0049 | 0.9648 ± 0.0207 |
| LTX-Video 2.0 | 0.9951 ± 0.0054 | 0.9961 ± 0.0017 | 0.9329 ± 0.0712 |
| HunyuanVideo 1.5 | 0.9687 ± 0.0236 | 0.9868 ± 0.0092 | 0.9394 ± 0.0267 |
| Wan 2.2 | 0.9804 ± 0.0150 | 0.9900 ± 0.0054 | 0.9537 ± 0.0283 |

### Data Augmentation

Retained videos were augmented to increase dataset variance before being used for Vision Teacher fine-tuning:
- **Horizontal flipping** — enforces spatial invariance
- **Temporal scaling** — fast/slow speed shifts to enforce temporal invariance

---

## How This Dataset Was Used

The cross-modal distillation framework operates in four sequential phases:

```
Text Prompts
     │
     ▼
Video Generation (LTX 2.0 / 2.3, Wan 2.2, HunyuanVideo 1.5)
     │
     ▼
VBench Quality Filter (threshold ≥ 0.80) ──► Quarantined videos discarded
     │
     ▼
Data Augmentation (flip + temporal scale)
     │
     ▼
Vision Teacher Fine-Tuning
     │
     ▼
Individual Knowledge Banks (one per generative model)
     │
     ▼
Ensemble Knowledge Bank (averaged probability distributions)
     │
     ▼
Cross-Modal Student Distillation
(BiLSTM / 2D CNN / Lightweight CNN trained on scarce IMU data
 + soft labels from Knowledge Bank via blended KL + CE loss)
```

The **Ensemble Knowledge Bank** aggregates the soft-label probability distributions from all four generative models to mitigate the individual domain biases of each generator, consistently outperforming any single-model teacher across all scarcity levels.

---

## Key Results

Performance improvement over a purely physical IMU baseline (Macro F1-Score, BiLSTM student):

| Data Availability | Physical Baseline | Ensemble Distilled | Δ |
|-------------------|------------------|--------------------|---|
| 0.1% | 17.55% ± 4.69% | — | — |
| 1% | 28.37% ± 3.57% | 30.93% ± 5.13% | **+2.56%** |
| 25% | 72.21% ± 7.08% | 77.73% ± 3.73% | **+5.52%** |
| 100% | 90.96% ± 0.57% | 91.11% ± 0.86% | +0.15% |

> At 25% scarcity, the **Lightweight CNN** student achieved the highest gain: **+16.09% Macro F1-Score**.

---

## Physical Datasets Reference

This synthetic dataset was designed to complement and taxonomically align with the following open-source physical HAR datasets:

- **PAMAP2** — Reiss, A. and Stricker, D. (2012). *Introducing a New Benchmarked Dataset for Activity Monitoring.* ISWC.
- **SisFall** — Sucerquia, A., López, J. D., and Vargas-Bonilla, J. F. (2017). *SisFall: A Fall and Movement Dataset.* Sensors, 17(1), 198.

---

## Citation

If you use this dataset in your research, please cite:
(under revision)
```bibtex
@article{monteiro2026crossmodal,
  title   = {Cross-Modal Knowledge Distillation for Edge HAR via Zero-Shot Text-to-Video Diffusion Models},
  author  = {Monteiro, Marcos Eduardo Pivaro and Farhat, Jamil de Araujo and Nassu, Bogdan Tomoyuki and Fabro, João Alberto},
  journal = {IEEE Access},
  year    = {2026},
  institution = {Federal University of Technology -- Paraná (UTFPR), Brazil}
}
```

---

## License

This dataset is released under the [Creative Commons Attribution 4.0 International (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/) license. You are free to share and adapt the material for any purpose, provided appropriate credit is given.

