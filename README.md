# Gauge-Invariant Representation Holonomy

Project page for the paper:

**Gauge-invariant representation holonomy**  
Vasileios Sevetlidis and George Pavlidis  
Athena Research Center

Site: <https://vasiseve.github.io/Gauge-Invariant-Representation-Holonomy/>
Code: <https://github.com/vasiseve/gauge-invariant-representation-holonomy-code>

## About

This repository hosts the Vue/GitHub Pages companion site for the paper. The
site presents the motivation, geometric intuition, estimator, theoretical
properties, selected figures, FAQ, and the manuscript PDF.

Representation holonomy is a gauge-invariant statistic for measuring how neural
network features change along closed input paths. Instead of comparing
activation sets only at fixed samples, the method estimates local
rotation-only transports between neighboring feature geometries, composes them
around a loop, and measures the residual twist.

## Repository Scope

This repository is intended for the project page and publication materials.

For a full reproducibility release, it is usually cleaner to use a separate
code repository containing:

- training and evaluation scripts,
- estimator implementation,
- experiment configs,
- plotting scripts,
- environment files,
- and instructions for regenerating tables and figures.

That code repository can then be linked from this page and from the paper. If
the codebase is small, it can also live here under a `code/` or `experiments/`
directory, but a separate repository keeps the website lightweight.

## Local Development

```bash
npm install
npm run dev
```

## Build

```bash
npm run build
```

## GitHub Pages

This site is deployed with GitHub Actions. The workflow builds the Vue app and
uploads the generated `dist/` directory to GitHub Pages.

In the repository settings, use:

```text
Settings -> Pages -> Build and deployment -> Source: GitHub Actions
```

## Citation

```bibtex
@inproceedings{sevetlidis2025gauge,
  title     = {Gauge-invariant representation holonomy},
  author    = {Sevetlidis, Vasileios and Pavlidis, George},
  booktitle = {International Conference on Learning Representations},
  year      = {2025}
}
```
