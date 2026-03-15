---
layout: post
title: "Make Anything with LEGO: Building LEGO 3D Blueprints from 2D Images"
date: 2026-03-15 09:30:00 +0900
categories: [project, generative-ai]
---

This post summarizes the README content for **Make Anything with LEGO**, with images from the project.

## Motivation

The project is inspired by the *Neverland Syndrome* trend (Stay / Return / Play). Focusing on **Return** and **Play**, the team designed a service that turns a photo of a desired object into a LEGO blueprint, enabling users to build custom LEGO creations beyond existing designs.

## Research Process

### Image2LEGO (Baseline)

![image2lego](https://github.com/KHAI-2023/Make_Anything_with_LEGO/assets/64340624/f9ab4104-4da6-4866-a0b9-6bb31cfde274)

The original Image2LEGO architecture used a 3D autoencoder and a 2D image encoder, training latent alignment with MSE and reconstruction with BCE. Limitations were:
- **Limited generalization** to unseen categories
- **Background sensitivity** (requires background-free images)
- **Slow inference**, not ideal for a user-facing service

### One-2-3-45 (Adopted Model)

<img width="807" alt="One-2-3-45" src="https://github.com/KHAI-2023/Make_Anything_with_LEGO/assets/64340624/7f2c1422-856d-4a8d-a872-f354f0f700eb">

The One-2-3-45 model addresses common 3D reconstruction issues:
- Time-consuming iterative reconstruction
- Memory-intensive high-resolution processing
- Inconsistent 3D geometry from occluded views
- Poor mesh quality

It synthesizes multi-view images (Zero123), performs pose estimation on nearby views, and reconstructs a 360° mesh with a **single feed-forward pass**, reducing inference time to **~45 seconds**.

### Super Resolution

<img width="807" alt="Super Resolution" src="https://github.com/KHAI-2023/Make_Anything_with_LEGO/assets/64340624/774d9205-6432-4c4c-b69b-eb49f0d82dda">

To mitigate low-quality inputs, super-resolution is applied before segmentation. The team uses a **Stable Diffusion**-based approach to upsample and denoise images.

<img width="1001" alt="Diffusion Model" src="https://github.com/KHAI-2023/Make_Anything_with_LEGO/assets/127406760/c1831c3b-1e19-4e3b-b6d8-fdad01a24062">

### SR + One-2-3-45 (Final Pipeline)

<img width="807" alt="SR + One-2-3-45" src="https://github.com/KHAI-2023/Make_Anything_with_LEGO/assets/64340624/d32e7492-a666-4eac-8970-fe18c4045d68">

Final pipeline:
1. Super-resolution & denoising
2. Object segmentation
3. 3D mesh reconstruction (One-2-3-45)
4. Voxelization
5. LEGO blueprint generation via **ColouredVoxels2LDR**

### ColouredVoxels2LDR

The algorithm converts each voxel into LEGO bricks, groups layers for efficient assembly, and maps extracted colors to bricks.

## Results

### Demo Video

[https://www.youtube.com/watch?v=c-NsbsgWnhQ&t=9s](https://www.youtube.com/watch?v=c-NsbsgWnhQ&t=9s)

### Example Output

![example](https://github.com/KHAI-2023/Make_Anything_with_LEGO/assets/127406760/166cb221-4b87-4bbe-b343-a8f117cf7aa9)

The output shows the progression: segmentation → 3D mesh → voxelization → LEGO algorithm.

### Research Value

- Better outputs by combining **Super Resolution + One-2-3-45**
- More generalizable than Image2LEGO across categories
- Extendable to **text-to-image** → LEGO pipeline

### Commercial Value

The Korean "kidult" market grew from ~500B KRW (2014) to ~1.6T KRW (2020), with projections up to ~11T KRW. This project targets that market by enabling custom LEGO designs from user-provided images, useful for personalization, marketing, and rapid prototyping.

## References

- Image2Lego: https://arxiv.org/pdf/2108.08477.pdf
- High-Resolution Image Synthesis with Latent Diffusion Models: https://arxiv.org/pdf/2112.10752.pdf
- One-2-3-45: https://arxiv.org/pdf/2306.16928.pdf
- ColouredVoxels2LDR: https://github.com/pennyforge/ColouredVoxels2LDR
- Diffusers: https://github.com/huggingface/diffusers
- Project repo: https://github.com/coqls1229/Make_Anything_with_LEGO
