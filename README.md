#  Improving GPR Image Resolution Using a Transformer-UNet with a Hybrid Loss  

[![PyTorch](https://img.shields.io/badge/Framework-PyTorch-red)](https://pytorch.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![Paper](https://img.shields.io/badge/Paper-IEEE%20Under%20Review-blue)](https://github.com/)
[![Dataset](https://img.shields.io/badge/Data-gprMax%20Simulated-orange)](https://github.com/gprMax/gprMax)

---

##  Problem Statement

Ground Penetrating Radar (GPR) is a non-destructive technique for subsurface imaging used in civil, defense, and geophysical applications.  
However, GPR image resolution is **limited by bandwidth, attenuation, and noise**, restricting detection of small or deep features.  

Existing super-resolution models often:
- Depend on **narrow-band or frequency-specific data**
- Lack **spectral generalization**
- Introduce **artifacts** during reconstruction  

This project proposes a **Transformer-UNet hybrid** trained on synthetic GPR data to enhance image resolution across multiple unseen frequency bands — **without hardware upgrades**.

---

##  Literature Overview (Brief)

| Work | Contribution |
|------|---------------|
| Donini et al. (2024) | CycleGAN-based radar sounder super-resolution |
| Liu et al. (2024) | Cycle-consistent GAN for unpaired radar data |
| Xiong et al. (2023) | GPR-GAN with adaptive discriminator augmentation |
| Kang et al. (2020) | 3D GPR super-resolution with f–k analysis |
| Zwartjes et al. (2024) | 2D U-Net for seismic bandwidth extension |
| Junkai et al. (2024) | GPR-TransUNet for subsurface permittivity inversion |

 *Proposed work extends these ideas by combining Transformer attention with spectral-domain loss wit Huber loss for GPR bandwidth enhancement.*

---

##  Dataset Simulation

Synthetic radargrams were generated using **gprMax**, an open-source electromagnetic simulator.

- **Primary dataset:** 8,300 paired B-scans (750 MHz ↔ 1250 MHz)
- **Validation:** 700–1167 MHz, 800–1333 MHz, 850–1416 MHz, 900–1500 MHz, 650–1083–1800 MHz
- **Additional:** 2,100 pairs (400–670 MHz) for cross-band training

Each sample includes randomized object shapes and dielectric properties with 10 soil variations to mimic realistic environments.

---

##  Methodology

###  Architecture Overview

The **Transformer-UNet with UpCUP Decoder** combines:
- CNN Encoder – local feature extraction  
- Transformer Bottleneck – global contextual modeling  
- CUP Decoder – Cascaded Upsampling via Convolution + Pixel Shuffle  
- Skip Connections – detail preservation  

<img width="809" height="778" alt="image" src="https://github.com/user-attachments/assets/4ccad614-3754-4d12-a193-263a3e72e36f" />

*Figure: Proposed Transformer-UNet architecture with Transformer bottleneck and CUP-based upsampling.*

---

###  Training Configuration
| Parameter | Value |
|------------|--------|
| Framework | PyTorch |
| Optimizer | Adam |
| Learning Rate | 1e-4 |
| Epochs | 75 |
| Batch Size | 8 |
| GPU | NVIDIA RTX 5080 (16 GB) |

---

##  Baseline & Comparison Models

| Model | Key Features | Loss |
|--------|---------------|------|
| Simple U-Net | 3-Level encoder-decoder | Huber |
| Attention U-Net | Attention gates in skip connections | Huber |
| Attention U-Net (ResBlocks) | 6 residual blocks | Huber / Huber+SSIM |
| TransUNet | Transformer bottleneck | Huber / Huber+SSIM |
| **Proposed Model (Ours)** | Transformer + CUP Decoder + Hybrid (Huber + f–k) Loss | ✅ |

---

##  Results Summary

| Frequency Band (MHz) | SSIM | PSNR (dB) | Model |
|-----------------------|------|-----------|--------|
| 700–1167 | 0.9871 | 38.43 | Attention U-Net (Hybrid) |
| 800–1333 | **0.9925** | **41.44** | **Proposed Model** |
| 850–1416 | 0.9698 | 36.40 | Proposed Model |
| 900–1500 | 0.9388 | 35.19 | Proposed Model |
| 650–1083–1800 | 0.9508 | 28.89 | Proposed Model |
| 400–670 | 0.9583 | 42.84 | Proposed Model |

>  The proposed model improved dominant frequency by up to **60 %** and achieved **SSIM ≈ 0.93–0.99**, showing strong spatial and spectral fidelity across unseen bands.

---

##  Conclusion

This work introduces a **Transformer-UNet with Hybrid Huber + f–k Loss** for GPR image super-resolution.  
It effectively combines convolutional locality, Transformer global context, and spectral regularization to achieve high-fidelity radargrams across diverse frequencies.  

**Key Highlights:**
- Frequency generalization without retraining  
- Consistent structural similarity (SSIM > 0.93)  
- Two-stage enhancement boosts bandwidth effectively  

---

##  Future Work
- Extend evaluation to **real-world field GPR data**  
- Develop **multi-frequency transfer learning** modules  
- Explore **3D GPR volumetric imaging**  

---
