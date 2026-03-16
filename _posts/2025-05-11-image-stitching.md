---
layout: post
order: 3
title: "Implementation of Image Stitching"
date: 2025-05-11
categories: [project, vision]
---

This post summarizes the project **Implementation of Image Stitching**: implementing an end-to-end image stitching pipeline with minimal library usage (corner detection → matching → homography → warping → panorama).

## Project Background

- Topic: implementing the **Image Stitching** process end-to-end
- Constraint: minimize use of libraries (allowing basic I/O and math); using a library for **SVD** in homography computation is permitted
- Environment: Python (VSCode)

## Pipeline Summary

### 1) Image preprocessing (denoising)

Gaussian smoothing is applied to reduce noise. Padding is added so the kernel can be applied near borders.

<img src="{{ '/assets/img/projects/image-stitching/preprocessing.png' | relative_url }}" width="720" />

### 2) Corner detection (Harris)

**Harris Corner Detection** is used to find points with large gradients in both x/y directions.
- Gradients: computed with **Sobel** filters
- Noise robustness: smoothed via a mean/gaussian-like filtering step on the structure tensor terms
- Corners: selected by thresholding the Harris response

<img src="{{ '/assets/img/projects/image-stitching/corner_detection.png' | relative_url }}" width="720" />

### 3) Point matching (NCC)

For each corner, a local patch is compared using **NCC (Normalized Cross-Correlation)**. Only pairs above a threshold are kept as candidate matches.

During testing, matching became too slow when too many corners were detected, so the corner threshold was increased and the number of corners was capped (e.g., top-N) to reduce computation.

<img src="{{ '/assets/img/projects/image-stitching/point_matching.png' | relative_url }}" width="720" />

### 4) Outlier rejection (RANSAC)

**RANSAC** is used to remove outlier matches and keep inliers consistent with a single transform.

<img src="{{ '/assets/img/projects/image-stitching/ransac.png' | relative_url }}" width="720" />

### 5) Homography computation + warping

Using inlier correspondences, the **homography** is estimated, and warping is applied to map the second image into the first image’s coordinate system for verification.

<img src="{{ '/assets/img/projects/image-stitching/warping.png' | relative_url }}" width="720" />

### 6) Image stitching (panorama)

Finally, the stored homography is used to create a panorama. Overlapping regions are blended with a simple feathering (averaging) strategy.

<img src="{{ '/assets/img/projects/image-stitching/stitching.png' | relative_url }}" width="720" />

## How to Run

From the project directory (with `img1.JPG` and `img2.JPG` present), run:

```bash
python main.py
```

## Project Link

- https://github.com/coqls1229/implementation-of-image-stitching
