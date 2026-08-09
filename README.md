# Synthetic IMU Data Generation via Diffusion-Based Video Models for Enhanced HAR

[![License: CC BY 4.0](https://img.shields.io/badge/License-CC%20BY%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by/4.0/)
[![Paper](https://img.shields.io/badge/Paper-under%20revision-blue)](https://ieeeaccess.ieee.org/)
[![Institution](https://img.shields.io/badge/Institution-UTFPR-green)](https://www.utfpr.edu.br/)

This repository contains synthetic human-activity video data generated for the paper:

> **"Synthetic IMU Data Generation via Diffusion-Based Video Models for Enhanced HAR"**
> Marcos Eduardo Pivaro Monteiro, Jamil de Araujo Farhat, Bogdan Tomoyuki Nassu, and Joao Alberto Fabro
>
> Under revision

The data supports research on Human Activity Recognition (HAR) and cross-modal learning between generated video and inertial measurement unit (IMU) data.

## Dataset Structure

```
synthetic_dataset/
|-- hunyuan_1_5_720/
|-- ltxv2_720/
|-- ltxv2_3_720/
`-- wan2_2_720/

synthetic_dataset_extra/
|-- hunyuan_1_5_720/
|-- ltxv2_720/
|-- ltxv2_3_720/
`-- wan2_2_720/
```

Both dataset folders contain videos from the same four diffusion-based video models. The folders group videos by model and use descriptive filenames that identify the activity class and environment.

`synthetic_dataset` is the primary corpus, generated with seed `42`. `synthetic_dataset_extra` is an additional corpus with the same activity and environment coverage, generated with seed `654657`. It is provided separately so that users can distinguish the additional generation set from the primary corpus.

## Video Models

| Folder | Model | Version |
|---|---|---|
| `hunyuan_1_5_720` | [HunyuanVideo](https://github.com/Tencent/HunyuanVideo) | 1.5 |
| `ltxv2_720` | [LTX-Video](https://github.com/Lightricks/LTX-Video) | 2.0 |
| `ltxv2_3_720` | [LTX-Video](https://github.com/Lightricks/LTX-Video) | 2.3 |
| `wan2_2_720` | [Wan](https://github.com/Wan-Video/Wan2.2) | 2.2 |

## Activity Labels

The filenames use the following activity labels:

| Label | Activity |
|---|---|
| Class A | Running |
| Class B | Fall |
| Class C | Lifting |
| Class D | Stairs |
| Class E | Jumping |
| Class F | Stumble |
| Class G | Nordic Walking |
| Class H | Soccer |

## Generation Details

Videos were generated from activity prompts combined with environmental variations. The prompt formulation requests a full-body view of the person, and the generated videos use portrait orientation at 720p resolution and 24 frames per second.

The generation workflow includes visual-quality screening with [VBench](https://github.com/Vchitect/VBench) and data augmentation through horizontal flipping and temporal scaling.

## Citation

The paper is under revision. Citation details will be updated when publication information is available.

```bibtex
@article{monteiro2026syntheticimu,
  title   = {Synthetic IMU Data Generation via Diffusion-Based Video Models for Enhanced HAR},
  author  = {Monteiro, Marcos Eduardo Pivaro and Farhat, Jamil de Araujo and Nassu, Bogdan Tomoyuki and Fabro, Joao Alberto},
  journal = {IEEE Access},
  year    = {2026},
  note    = {Under revision}
}
```

## License

This dataset is released under the [Creative Commons Attribution 4.0 International (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/) license. You are free to share and adapt the material for any purpose, provided appropriate credit is given.
