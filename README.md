# Sozialinfo Helm Charts

This repository contains Helm charts for Sozialinfo applications.

## Usage

Add this Helm repository:

```bash
helm repo add sozialinfo https://sozialinfo.github.io/helm-charts/
helm repo update
```

## Available Charts

- **sozialinfoOdoo** (v0.1.0): Odoo deployment with Sozialinfo customizations

## Installation

```bash
helm install my-odoo sozialinfo/sozialinfoOdoo
```

## Development

To add a new chart version:

1. Package the chart: `helm package <chart-directory>`
2. Update the index: `helm repo index . --url https://sozialinfo.github.io/helm-charts/`
3. Commit and push changes

GitHub Pages will automatically serve the repository at `https://sozialinfo.github.io/helm-charts/`.
