# SPHERE

**SPHERE** is a macOS desktop app for generating and evaluating privacy-preserving synthetic tabular data. It runs entirely on your Mac — no data leaves your machine.

---

## Features

- **Synthetic data generation** — generate privacy-preserving synthetic versions of your tabular CSV datasets
- **Fidelity evaluation** — compare mean, variance, correlation structure, and marginal distributions between real and synthetic data, scored 0–100
- **Privacy risk assessment** — measure singling-out, linkability, and inference attack risk, normalised against a baseline so scores are meaningful across datasets
- **AI-powered analysis** — ask questions about your data in natural language; Claude writes and runs analysis code on your behalf using your own API key
- **Cloud sharing** — share synthetic datasets and evaluation certificates via Dropbox or S3-compatible storage; your data never passes through SPHERE's servers

---

## Download

Download the latest release from the [Releases](https://github.com/statzihuai/SPHERE/releases) page.

---

## Requirements

- macOS 12 (Monterey) or later
- Apple Silicon (M1 / M2 / M3 / M4)
- A SPHERE license key — contact [zihuai@stanford.edu](mailto:zihuai@stanford.edu) or visit [sphere.stanford.edu](https://sphere.stanford.edu)
- An [Anthropic API key](https://console.anthropic.com) for AI-powered analysis (optional)

---

## Installation

1. Download `SPHERE-0.1.0-arm64.dmg` from the [latest release](https://github.com/statzihuai/SPHERE/releases/latest)
2. Open the DMG and drag **SPHERE** to your Applications folder
3. Launch SPHERE and enter your license key when prompted

---

## Evaluation

SPHERE produces two independent scores for every synthetic dataset:

| Score | What it measures |
|---|---|
| **Fidelity** | How closely the synthetic data reproduces the statistical properties of the real data — means, variances, correlations, and marginal distributions — on a 0–100 scale |
| **Privacy** | How resistant the synthetic data is to singling-out, linkability, and inference attacks, normalised against a baseline so that 100 means no measurable excess risk |

---

## CLI companion

For pipelines, HPC environments, or scripting, the SPHERE algorithm is also available as a standalone command-line tool:

```sh
npm install -g sphere-cli
sphere generate real.csv -o synth.csv
sphere evaluate real.csv synth.csv
```

→ [sphere-cli on GitHub](https://github.com/statzihuai/sphere-cli) · [sphere-cli on npm](https://www.npmjs.com/package/sphere-cli)

---

## License

Copyright © 2026 Zihuai He, Stanford University. All rights reserved.
