# SPHERE

**Privacy-preserving synthetic twins of sensitive tabular data — generated, evaluated and analysed on your Mac, without your real records leaving it.**

[![DOI](https://img.shields.io/badge/DOI-10.64898%2F2026.09.01.748580-blue)](https://doi.org/10.64898/2026.09.01.748580)
[![Latest release](https://img.shields.io/github/v/release/statzihuai/SPHERE)](https://github.com/statzihuai/SPHERE/releases/latest)
[![License: non-commercial academic](https://img.shields.io/badge/license-non--commercial%20academic-lightgrey)](LICENSE)

[Website](https://www.sphereworld.ai/?ref=github-sphere) · [Download for Mac](https://www.sphereworld.ai/download/mac?ref=github-sphere) · [Preprint](https://doi.org/10.64898/2026.09.01.748580) · [Privacy Handbook](HANDBOOK.md) · [Validation](https://www.sphereworld.ai/validation/?ref=github-sphere) · [SPHERE World catalog](https://sphere-world.vercel.app)

**SPHERE** is a macOS desktop app for generating, evaluating and analysing privacy-preserving synthetic data — a *SPHERE twin* of your real data matrix that keeps its statistical structure without carrying its records.

Your real data is read locally and never uploaded. See [What leaves your Mac](#what-leaves-your-mac) for the precise boundary.

> **Beta.** SPHERE is in active development. Please read the [Privacy Handbook](HANDBOOK.md) before using it with sensitive data.

---

## Quickstart

1. **Download** SPHERE for Mac from [sphereworld.ai/download/mac](https://www.sphereworld.ai/download/mac?ref=github-sphere) (always the latest release), or take the `SPHERE-<version>-arm64.dmg` from the [latest GitHub release](https://github.com/statzihuai/SPHERE/releases/latest).
2. **Install** — open the DMG, drag **SPHERE** to your Applications folder and launch it.
3. **Sign in** with a free SPHERE account — email, or one click with Google or Microsoft. The first time, you confirm your profile and accept the SPHERE use agreement.
4. **Try the example data** included with the app: generate a twin, then evaluate it. When you are ready, do the same with your own CSV.

You need macOS 12 or later on Apple Silicon and an internet connection — see [Requirements](#requirements). The app is signed and notarized by Apple. Updates download automatically; click **Update ready · Restart** in SPHERE to install one.

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

**Your conversation is routed through SPHERE.** The AI features do not talk to Anthropic directly — requests go through SPHERE's proxy so usage can be metered, and the conversation is retained with your account. This applies whether SPHERE pays for the model or you bill it to your institution's own Anthropic account.

**Generating, evaluating and certifying need a connection.** The work runs on your Mac, but before each generate, evaluate or certificate save (including saving a ZIP, which holds the certificate) starts, the app asks SPHERE's server to approve it. The request is tied to your account and contains only a random request ID, the step, the row and column counts and file size where the app knows them before the step (in practice your original file's size for generate and evaluate), and the app version. If SPHERE can't be reached, the app retries for up to about 30 seconds, then stops the step before any work is done and says so. Signing in still needs a connection the first time, and again once 7 days have passed since the app last confirmed your sign-in with SPHERE; within those 7 days an app without a connection stays signed in but cannot generate, evaluate or certify.

**A small usage record is kept.** After each generate, evaluate, certify or share, the app records which step it was (and, for a share, where to), when it happened, how long it took (for generate and evaluate), the row and column counts and the file size where they apply, the app version and, for generate, evaluate and certify, the ID of the step's approval, and sends that to SPHERE with your account the next time it is online with that account signed in. For generate and evaluate the row and column counts and the file size are your original file's (a twin has the same number of rows and columns as the original); for a share they describe the synthetic file. It never contains your data, file names, paths or column names.

The [Privacy Handbook](HANDBOOK.md) documents this in full, including a **Limits you should know about** section stating plainly what the design does *not* guarantee.

---

## Requirements

- macOS 12 (Monterey) or later
- Apple Silicon (M1 / M2 / M3 / M4)
- A free SPHERE account (sign-in is required). The first time, you confirm your name, institution, organization type and role and accept the SPHERE use agreement (research use only, no commercial use); the app and the website share the account, so you do this once.
- An internet connection to generate, evaluate and certify (the work runs locally; SPHERE approves each step first)

SPHERE AI is free for academic research during the research preview, within a monthly usage limit that resets on the 1st; commercial use needs a licence. The free usage limit may change or end with notice, and SPHERE AI can pause for everyone when the monthly research budget is reached.

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

## Citation

If you use SPHERE in your research, please cite the preprint:

> Zihuai He, Junyoung Park, Rafael Catoia Pulgrossi, Justin Lee, Robert R. Butler III, Audrey Weber, Lu Tian, Xiang Zhang, Julie Fangran Wang, Sharon Sha, Elizabeth C. Mormino, Tony Wyss-Coray, Victor W. Henderson, Frank M. Longo, James Zou, Manisha Desai, Russ Altman. **Unlocking Sensitive Data with SPHERE in the Age of AI.** *bioRxiv* (2026). [doi:10.64898/2026.09.01.748580](https://doi.org/10.64898/2026.09.01.748580)

```bibtex
@article{He2026SPHERE,
  title   = {Unlocking Sensitive Data with {SPHERE} in the Age of {AI}},
  author  = {He, Zihuai and Park, Junyoung and Pulgrossi, Rafael Catoia and Lee, Justin and
             Butler, III, Robert R. and Weber, Audrey and Tian, Lu and Zhang, Xiang and
             Wang, Julie Fangran and Sha, Sharon and Mormino, Elizabeth C. and Wyss-Coray, Tony and
             Henderson, Victor W. and Longo, Frank M. and Zou, James and Desai, Manisha and Altman, Russ},
  journal = {bioRxiv},
  year    = {2026},
  doi     = {10.64898/2026.09.01.748580},
  url     = {https://doi.org/10.64898/2026.09.01.748580},
  note    = {Preprint}
}
```

The same reference is in [`CITATION.cff`](CITATION.cff), which GitHub's **Cite this repository** button reads.

---

## This repository

This repo distributes the app: the handbook, examples, citation metadata and release binaries. The application source is not public. The "Source code" archive GitHub attaches to each release contains only the files listed here.

---

## License

Copyright © 2026 Zihuai He, Stanford University. All rights reserved.

Free for non-commercial academic research and evaluation; commercial use
requires a separate written license. A patent application relating to the
underlying method is pending. See [LICENSE](LICENSE).

For licensing inquiries: zihuai@stanford.edu
