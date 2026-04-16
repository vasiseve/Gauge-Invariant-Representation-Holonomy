# Gauge-invariant representation holonomy

Vue project page for the paper **Gauge-invariant representation holonomy**.

## Local development

```bash
npm install
npm run dev
```

## Build

```bash
npm run build
```

## GitHub Pages

This repository includes a Pages workflow at `.github/workflows/deploy.yml`.
After pushing to `main`, enable GitHub Pages with **Build and deployment: GitHub Actions** in the repository settings.

## First publish from this folder

```bash
git init
git add .
git commit -m "Create holonomy project page"
git branch -M main
git remote add origin git@github.com:<your-user>/GaugeInvariantRepresentationHolonomy.git
git push -u origin main
```

Create the empty GitHub repository before adding the remote. If you choose a
different repository name, update the `base` value in `vite.config.js` to match
the Pages path.
