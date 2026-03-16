---
layout: post
order: 1
title: "3D Model Rendering with OpenGL"
date: 2025-12-16
categories: [project, graphics]
---

This post summarizes the project **3D Model Rendering with OpenGL** (loading and rendering textured 3D assets by parsing point, normal, polygon, and material information).

<img src="{{ '/assets/img/projects/opengl-plotting.png' | relative_url }}" width="720" />

## Project Overview

The project focuses on rendering a small scene in OpenGL by:
- Loading geometry from `.obj` files (vertices, normals, texture coordinates, faces)
- Parsing materials from `.mtl` files (e.g., `Ns`, `Ka`, `Kd`, `Ks`, `Ke`, `Ni`, `d`) and applying textures
- Providing interactive camera/object control for inspection

## Key Implementation Points

### Ground construction

The ground is constructed from a textured model. The renderer reads the ground material configuration from its `.mtl` file and loads texture images via `stb_image`.

### Loading and placing multiple objects

To make scene composition easier, coordinate axes are drawn as a reference and objects are translated/rotated into place (e.g., bench as a base object, then other props positioned along axes).

Camera setup used in the project:
- Eye: `(0, 2, 2)`-ish range (adjusted for the scene)
- Look-at: toward the scene center `(0, 0, 0)`

## Additional Feature: Catmull–Clark Subdivision

Subdivision is implemented using a Catmull–Clark-style approach by computing:
- Face centers
- Edge midpoints

This increases polygon density to produce smoother-looking surfaces.

## Project Link

- https://github.com/coqls1229/3d-model-rendering-with-opengl
