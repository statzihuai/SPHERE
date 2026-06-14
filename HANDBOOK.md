# SPHERE App — User Handbook

## What is SPHERE?

SPHERE (Synthetic Privacy-preserving Honest Evaluation and Release Engine) is the end-to-end platform for generating, certifying, and sharing privacy-preserving synthetic data:

1. **Generate** — produce a synthetic twin of your sensitive dataset
2. **Evaluate & Certify** — formally score fidelity and privacy, issue a certificate
3. **Share** — upload the synthetic data to Dropbox or Zenodo
4. **Post to Catalog** — list the dataset in the SPHERE World public directory
5. **Analyse with AI** — use Claude to develop and run analyses on the synthetic data, then apply the resulting code to your real dataset locally

All five steps are governed by one rule: **real data never leaves your machine.**

---

## Privacy Architecture

Understanding the privacy boundaries is central to using SPHERE confidently. This section covers all five panels at once so you know exactly what stays local and what leaves.

### 100% Local — Never Leaves Your Computer

| Item | Why it stays local |
|---|---|
| Your original (real) data | Never read beyond your local disk. No upload, no transmission, no copy. |
| Synthesis algorithm | Runs entirely in the app process on your machine. |
| Evaluation computation | All statistical comparisons (fidelity, privacy tests) run locally. |
| SPHERE certificate | Generated locally, saved as an HTML file on your disk. |
| API keys (Claude, Dropbox, Zenodo) | Stored encrypted via macOS Keychain. Never sent to SPHERE's servers. |
| Column names, dimensions & scores used in AI auto-fill | Derived from the **synthetic** file only — not the real one. |
| analysis.py and all session figures | Written and executed entirely inside an isolated local sandbox. |

### What Is Uploaded (Synthetic Data Only)

When you share a dataset, **only the synthetic CSV** is uploaded — never the real data.

- **Dropbox**: Synthetic CSV and certificate go to your Dropbox account via OAuth. SPHERE has no access beyond the folder it creates.
- **Zenodo**: Synthetic CSV and certificate go to your Zenodo account via your personal API token.

### What the SPHERE World Catalog Receives

When posting to the public catalog, the API receives metadata only — no data rows, ever:

- Dataset title, description, domain, sharing terms
- Public Dropbox/Zenodo link to the *synthetic* file
- Fidelity and privacy scores (aggregate numbers only)
- Whether the dataset was SPHERE-generated or externally produced
- Whether a certificate exists · Storage method (Dropbox or Zenodo)

### What Claude Receives — Auto-Fill (Catalog Post)

The **Generate with AI** button in the Post panel sends a prompt to Claude (Anthropic API) containing:

1. The synthetic CSV **filename** (not its contents)
2. Column **names** from the synthetic header (up to 10, then "etc.")
3. **Dimensions** — row and column counts of the synthetic file
4. **Aggregate scores** — fidelity and privacy composites
5. Whether the dataset was SPHERE-generated or external

**What Claude never receives**: individual data records, cell values, your real data, or any information that could identify research participants.

### What Claude Receives — SPHERE AI Panel

In the SPHERE AI tab, Claude operates an agent loop over a Python sandbox. It receives:

- The **contents of the synthetic CSV** (Claude reads it via Python tools, e.g. `pd.read_csv`)
- Conversation messages and tool results (stdout/stderr from local Python execution)
- Figures it requests to view
- Files you explicitly attach in the chat

**What Claude never receives in SPHERE AI**: the real CSV, its path, its contents, or any rows from it. The real data path is stored in the app process only and is never included in any API message.

**"Deploy on real" does not involve Claude at all.** When you click that button, the app copies your real CSV into the sandbox, runs `analysis.py` as a local subprocess, then immediately deletes the copy. No call is made to the Claude API during this step. Claude's job was already done — it wrote the analysis code working only on the synthetic data.

> **Sandbox enforcement:** The Python sidecar runs under a macOS Seatbelt (sandbox-exec) profile that blocks all filesystem access outside the session sandbox directory. Even if the model were to write `open('/Users/you/real.csv')`, it would receive a `PermissionError`.

---

## The SPHERE Workflow

All five tabs form a single linear workflow. You do not have to complete every step — use as many as are relevant to your work.

### Step 1 — Generate Synthetic Data

**Tab: Generate**

1. Drag your real CSV into the app, or click to browse. The file is read locally — nothing is uploaded.
2. Configure generation parameters (synthetic rows, noise level, random seed).
3. Click **Generate**. The SPHERE algorithm runs on your machine and produces a synthetic CSV.
4. Download the synthetic CSV to a location of your choice.

*Your real data is only ever read from disk — it is never stored by the app or transmitted anywhere.*

---

### Step 2 — Evaluate & Certify

**Tab: Evaluate, Certify & Share**

1. Load your real CSV and the synthetic CSV to evaluate (SPHERE-generated or from any other tool).
2. Click **Evaluate**. The app computes:
   - **Fidelity scores** — how closely the synthetic data matches the real data's statistics (mean, variance, correlations, KS distance).
   - **Privacy scores** — how resistant the synthetic data is to singling-out, linkability, and inference attacks.
3. Review the scores and the visual report.
4. Click **Generate Certificate** to create a signed HTML evaluation report, saved to your disk.

*All computation happens on your machine. No data leaves during this step.*

---

### Step 3 — Share to Cloud

**Tab: Evaluate, Certify & Share → Share section**

Three options are available:

**Option A: Upload to Dropbox**
Connect via OAuth (direct with Dropbox, not SPHERE). Click **Share to Dropbox** — the synthetic CSV and certificate are uploaded to a new folder and a public link is generated.

**Option B: Upload to Zenodo**
Enter your Zenodo API token (stored locally via Keychain). Click **Upload to Zenodo** — the files are uploaded and a permanent DOI is generated.

**Option C: Save as ZIP**
Download a ZIP of the synthetic data and certificate for manual distribution. Nothing is uploaded.

---

### Step 4 — Post to SPHERE World Catalog

**Tab: SPHERE World (beta)**

Once your synthetic data is hosted on Dropbox or Zenodo, you can make it discoverable in the public catalog at [sphere-world.vercel.app](https://sphere-world.vercel.app).

1. On your dataset card, click **🌐 Post to SPHERE World**.
2. Fill in the title and description manually, or click **Generate with AI** to auto-fill. Set the domain and sharing terms:
   - **◎ Open (with citation)** — anyone can download; just cite your work.
   - **✉ Request access** — interested parties must email you first.
   - **⊙ Revenue share** — commercial licensing terms apply.
3. Click **Post**. Only metadata and the public link are sent — no data rows.
4. The button changes to **🌐 Update on SPHERE World**. Click it any time to change the title, description, or sharing terms. The same catalog record is updated in place — no duplicate is created.

**Deduplication**: Each dataset has a stable local UUID. Re-posting always updates the same catalog record. If you try to post a dataset whose title already exists in the catalog (posted by you or someone else), you will be asked to choose a different title.

---

### Step 5 — Analyse with SPHERE AI

**Tab: SPHERE AI (beta)**

SPHERE AI lets you build a rigorous data analysis by chatting with Claude — using the synthetic data as a safe stand-in throughout development. When the analysis is ready, the app runs it locally against the real data. **Claude is not involved in that final execution step at all.**

#### Setup

- **Anthropic API key** — obtained at `console.anthropic.com/settings/keys`. Entered once, stored encrypted via macOS Keychain.
- **Model** — Claude Opus 4.7 (most capable), Sonnet 4.6 (balanced), Haiku 4.5 (fastest). Changed in the Settings panel.

#### How the session works

1. **Load a synthetic CSV.** Click the green *Synthetic* chip and pick your CSV, or click a built-in example. An isolated sandbox folder is created and the synthetic file is placed inside it.
2. **Load the real CSV (optional, needed for deploy).** Click the red *Real* chip. This path is stored in the app process only — it is never shared with Claude.
3. **Chat with Claude.** Type a prompt and press Enter (⇧Enter for new line). Built-in starter prompts:
   - "Summarize the dataset: column types, missing values, and key distributions."
   - "Identify the strongest associations between variables and quantify them with effect sizes and p-values."
   - "Propose three hypothesis-driven analyses, then run the most informative one."
4. **Claude writes and runs code.** The agent iteratively writes Python in `analysis.py`, installs packages (`matplotlib`, `seaborn`, `statsmodels`…), and executes in the sandbox. Figures appear in the right panel. All execution is local.
5. **Iterate.** Ask follow-ups, change chart styles, add statistical tests. Claude refines `analysis.py` across turns. Click **Review analysis.py** to inspect the current script.
6. **Run on real data.** Once the synthetic run shows a green exit code, click **Deploy on real**. The app (not Claude):
   - Copies your real CSV into the sandbox as a temporary file.
   - Runs `analysis.py` locally as a subprocess against that copy.
   - Immediately deletes the copy when the script finishes.
   - Shows the real-data figures alongside the synthetic figures.

   No message is sent to Claude during this step. Claude already finished its work on the synthetic data.

7. **Save the session report.** After a successful run, a self-contained HTML report is generated containing the full conversation transcript, `analysis.py`, both figure sets, and an AI-written narrative summary. Click **Open report in sandbox** to reveal it in Finder.

#### File attachments

Click **+** in the chat input bar to attach files (PDFs, images, text/code). Attached files are sent to the Claude API as conversation context. Do not attach files containing real personal data.

#### Tools Claude can use

| Tool | What it does |
|---|---|
| `python` | Executes Python code in the sandbox. Output and errors are returned to Claude. |
| `pip_install` | Installs a Python package into the sandbox environment. |
| `read_file` | Reads a file from the sandbox directory. |
| `write_file` | Writes or overwrites a file in the sandbox directory. |
| `list_files` | Lists files present in the sandbox directory. |
| `view_figure` | Renders a saved figure so Claude can inspect and refine it. |

#### Buttons reference

| Button | What it does |
|---|---|
| **Send** | Sends your message to Claude. Press Enter (⇧Enter for new line). |
| **■ Stop** | Interrupts Claude at the next turn boundary. |
| **Review analysis.py** | Shows the current state of the analysis script in a dialog. |
| **Deploy on real** | App runs `analysis.py` locally against your real CSV. Claude is not contacted. |
| **Sandbox folder** | Opens the sandbox directory in Finder (scripts, figures, report). |
| **↺ Reset** | Clears the conversation and starts a fresh session with the same synthetic CSV. |

#### First-launch pre-loading

The first time the SPHERE AI tab is used, `matplotlib`, `seaborn`, and `statsmodels` are pre-installed in the sandbox Python environment. A progress banner is shown (typically 30–90 seconds). Subsequent sessions start immediately.

---

## Managing Your Datasets

### Syncing and Deleting

**Delete from Dropbox / Zenodo:** Click *Delete from Dropbox* (or Zenodo) on any dataset card. The app deletes the remote folder, removes the local history entry, and automatically removes the catalog listing.

**Sync:** Click **Sync** in the SPHERE World tab to reconcile your local history with remote storage. If a file was deleted outside the app (e.g. directly in Dropbox's web UI), the sync detects it and removes the stale entry and catalog listing.

### Updating a Catalog Listing

Click **🌐 Update on SPHERE World** on any dataset card to change the title, description, domain, or sharing terms. The existing catalog record is updated — no duplicate is created.

---

## SPHERE vs. External Synthetic

The app and catalog distinguish two dataset origins:

- **SPHERE** badge (cardinal red): The synthetic data was generated by the SPHERE algorithm built into this app.
- **External synthetic** badge (grey): The data was generated by a third-party tool (e.g., SDV, synthpop, CTGAN) and then evaluated using SPHERE's metrics.

This distinction is shown on dataset cards in both the app and the catalog.

---

## Data Flow Summary

```
Your real data (CSV)
        │
        ▼
  [SPHERE App — local only]
        │
        ├─► Synthesis algorithm ─────────────────────► Synthetic CSV (saved locally)
        │                                                      │
        ├─► Evaluation & scoring (local)                      ├─► Dropbox  (your account)
        │       │                                             └─► Zenodo   (your account)
        │       └─► Certificate HTML (local)                             │
        │                                                                 ▼
        ├─► AI auto-fill (catalog post)                        SPHERE World Catalog
        │     → Claude API receives: filename,                 (metadata + public link;
        │       column names, dimensions, scores                no real data, no rows)
        │
        └─► SPHERE AI panel
              ├─► Synthetic CSV → sandbox
              │       Claude writes analysis.py via python / pip / write_file tools
              │       All execution: local macOS Seatbelt sandbox
              │       Claude API receives: conversation + synthetic CSV contents only
              │
              └─► "Deploy on real" — app-only step, NO Claude API call
                      App copies real CSV → sandbox → runs analysis.py → deletes copy
                      Real data never transmitted; Claude is not involved in this step
```

---

## Frequently Asked Questions

**Q: Does SPHERE ever see my real data?**
No. Your real data is read locally by the app and never transmitted to SPHERE's servers, Anthropic, Dropbox, Zenodo, or any other service.

**Q: Can the synthetic data be traced back to real individuals?**
SPHERE evaluates exactly this risk using three attack models: singling-out (can you identify a unique individual?), linkability (can you link records across datasets?), and inference (can you predict a sensitive attribute?). The privacy scores reflect how resistant the synthetic data is to each attack. Higher scores mean greater protection.

**Q: Does Claude ever access my real data?**
No. In the SPHERE AI tab, Claude only ever sees the synthetic CSV and the code it writes itself. When you click "Deploy on real", the app — not Claude — copies your real CSV into the sandbox, runs `analysis.py` as a local subprocess, and immediately deletes the copy. No message is sent to the Claude API during this step.

**Q: Is it safe to use SPHERE AI with sensitive datasets?**
Yes — provided the synthetic data has good privacy scores. Claude only ever receives the synthetic CSV, which by design does not contain real individuals' records. Privacy scores above 70–80 indicate strong resistance to re-identification attacks.

**Q: Can Claude access files outside the sandbox?**
No. The Python sidecar runs under a macOS Seatbelt (sandbox-exec) profile that blocks all filesystem access outside the session sandbox directory. Any attempt returns a `PermissionError`.

**Q: What exactly is sent to Anthropic's API?**
In the SPHERE AI panel: conversation messages, tool results (stdout/stderr from local Python runs), figures Claude requests to view, and files you explicitly attach. The synthetic CSV's contents can be read by Claude via `pd.read_csv` inside the `python` tool. Do not use synthetic data with residual real values if you have concerns about Claude reading column contents.

In the catalog post panel (AI auto-fill): only the filename, column names, dimensions, and aggregate scores from the synthetic file.

**Q: Who can access the data on Dropbox or Zenodo?**
That depends on the sharing terms you set in the catalog. "Open (with citation)" means anyone with the link can access the synthetic file. "Request access" means interested parties must email you first.

**Q: What happens if I delete the dataset from Dropbox but it's in the catalog?**
The next time you run **Sync** in the app, the stale entry is detected and the catalog listing is removed automatically.

**Q: Can I update my catalog listing after posting?**
Yes. Click **🌐 Update on SPHERE World** on your dataset card at any time. The existing catalog entry is updated in place — no duplicate is created.

**Q: Are my API keys and tokens stored securely?**
All keys (Anthropic, Dropbox, Zenodo) are stored in your macOS user data directory, encrypted via macOS Keychain where supported. They are never sent to SPHERE's servers.

**Q: Can I evaluate a synthetic dataset generated by another tool?**
Yes. Use the Evaluate tab with any real CSV and any synthetic CSV, regardless of how the synthetic data was produced. It will be labelled "External synthetic" in the catalog.

---

## Quick Reference

| Action | Where | Privacy impact |
|---|---|---|
| Generate synthetic data | Generate tab | 100% local — nothing leaves your machine |
| Evaluate fidelity / privacy | Evaluate tab | 100% local |
| Generate certificate | Evaluate tab | 100% local |
| Upload to Dropbox / Zenodo | Share section | Synthetic data only |
| Post to SPHERE World catalog | SPHERE World tab → Post | Metadata + public link only |
| AI auto-fill description | Post panel → Generate with AI | Filename, column names, dimensions, scores sent to Claude |
| Update catalog listing | SPHERE World tab → Update | Same as Post — metadata only |
| Delete dataset | Card → Delete button | Removes from cloud storage + catalog |
| Sync | SPHERE World tab → Sync | Checks remote storage, removes stale entries |
| SPHERE AI — chat session | SPHERE AI tab | Synthetic CSV + conversation sent to Claude API |
| SPHERE AI — Deploy on real | SPHERE AI → Deploy on real | 100% local app step — Claude not contacted |
| Session report | SPHERE AI → Open report | 100% local — HTML saved to sandbox folder |
