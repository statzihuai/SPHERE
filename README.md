# SPHERE

**SPHERE** is a macOS desktop app for generating, evaluating and analysing privacy-preserving synthetic data matrix — a *SPHERE twin* of your real dataset that keeps its statistical structure without carrying its records.

Your real data is read locally and never uploaded. See [What leaves your Mac](#what-leaves-your-mac) for the precise boundary.

> **Beta.** SPHERE is in active development. Please read the [Privacy Handbook](HANDBOOK.md) before using it with sensitive data.

---

## Features

- **Synthetic data generation** — build a SPHERE twin of any data matrix CSV, on your machine
- **Fidelity evaluation** — mean, variance, correlation structure and marginal distributions compared against the real data, scored 0–100
- **Privacy risk assessment** — singling-out, linkability and inference attack risk, normalised against a baseline so scores are comparable across datasets
- **SPHERE AI** — ask questions in plain language; the assistant writes and runs analysis code against the twin, then re-runs the same script on your real data locally
- **SPHERE World** — publish a twin to a public catalog so others can find and cite it, on terms you set
- **Sharing** — distribute twins and evaluation certificates via Dropbox or Zenodo, or save them as a ZIP to share your own way

---

## What leaves your Mac

SPHERE AI is powered by a cloud model, so it is worth being exact about what it does and does not receive.

**Your real data is never sent.** The assistant develops its analysis against the SPHERE twin — synthetic data with the same schema and statistical shape. It has no access to the real file.

**When you deploy that analysis on your real data, the run happens locally.** The analysis code executes on your machine inside a sandbox with networking denied.

**Results are sent only if you ask for a written summary, and only after you have seen them.** SPHERE shows you the exact text that will be sent and waits for you to approve it. Aggregate statistics and figures are included; individual records from your real data are not.

**Your conversation is routed through SPHERE.** The AI features do not talk to Anthropic directly — requests go through SPHERE's proxy so usage can be metered, and the conversation is retained with your account. This applies whether you use SPHERE credits or your own Anthropic API key. Generation, evaluation and certification never need a network and work offline once you are signed in.

**A small usage record is kept.** After each generate, evaluate, certify or share, the app records which step it was (and, for a share, where to), when it happened, how long it took (for generate and evaluate), the row and column counts and the file size where they apply, and the app version, and sends that to SPHERE with your account the next time it is online with that account signed in. For generate and evaluate the row and column counts and the file size are your original file's (a twin has the same number of rows and columns as the original); for a share they describe the synthetic file. It never contains your data, file names, paths or column names.

The [Privacy Handbook](HANDBOOK.md) documents this in full, including a **Limits you should know about** section stating plainly what the design does *not* guarantee.

---

## Requirements

- macOS 12 (Monterey) or later
- Apple Silicon (M1 / M2 / M3 / M4)
- A free SPHERE account (sign-in is required)

For SPHERE AI you need either SPHERE credits or your own Anthropic API key. Generation, evaluation and sharing work without either.

---

## Installation

1. Download the `SPHERE-<version>-arm64.dmg` from the [latest release](https://github.com/statzihuai/SPHERE/releases/latest)
2. Open the DMG and drag **SPHERE** to your Applications folder
3. Launch SPHERE

The app is signed and notarized by Apple. Updates are delivered automatically.

---

## Evaluation

SPHERE produces two independent scores for every synthetic dataset:

| Score | What it measures |
|---|---|
| **Fidelity** | How closely the twin reproduces the statistical properties of the real data — means, variances, correlations and marginal distributions — on a 0–100 scale |
| **Privacy** | How resistant the twin is to singling-out, linkability and inference attacks, normalised against a baseline so that 100 means no measurable excess risk |

Every evaluation can be exported as a certificate recording both scores, the file fingerprints and the parameters used.

---

## CLI companion

For pipelines, HPC environments or scripting, the SPHERE algorithm is also available as a standalone command-line tool:

```sh
npm install -g sphere-cli
sphere generate real.csv -o synth.csv
sphere evaluate real.csv synth.csv
```

→ [sphere-cli on GitHub](https://github.com/statzihuai/sphere-cli) · [sphere-cli on npm](https://www.npmjs.com/package/sphere-cli)

---

## Examples

The [`examples/`](examples/) folder contains real NHANES-derived datasets with their SPHERE twins, certificates and parameter files — enough to see what the output looks like before running it on your own data.

---

## This repository

This repo distributes the app: the handbook, examples and release binaries. The application source is not public. The "Source code" archive GitHub attaches to each release contains only the files listed here.

---

## License

Copyright © 2026 Zihuai He, Stanford University. All rights reserved.

Free for non-commercial academic research and evaluation; commercial use
requires a separate written license. A patent application relating to the
underlying method is pending. See [LICENSE](LICENSE).

For licensing inquiries: zihuai@stanford.edu
