# Batch Scan & HTML Report

Point the addon at a **folder of exported assets** and get a single, shareable HTML report — health scores, an export-risk level, duplicate-geometry detection, and a quality trend across runs. This is the lead's view and the "send it to the team" deliverable.

---

## Running a batch scan

In the **Asset Preflight** window:

1. **Scope** tab → set **Batch folder** (the exports) and **Output** (where the report goes — empty = the batch folder). Toggle **Subfolders** and **Delta** as needed.
2. **Scan** tab → **Scan Folder**.
3. **Open HTML Report**.

Supported formats: **FBX · glTF · GLB · OBJ**.

Each file is imported into a **throwaway scene**, validated with the active **profile**, then removed — your open scene is never touched. **Import failures never stop the batch**; the file is flagged and the scan continues.

---

## The HTML report

A single self-contained `.html` file — **no internet, no dependencies, no telemetry**. Open it in any browser, drop it in Slack / Drive / email.

It contains:

- **Executive Summary** hero (top) — a big **Project Health** score, **Export Risk Level**, asset counts, and **budget % bars** (triangles / VRAM / materials). The screen a lead forwards on Slack / Jira. See [Budgets & Dashboard](budgets.md).
- **Thumbnails** — a rendered preview of each asset (toggle in Scope, or `--no-thumbnails` in the CLI). Embedded as base64, so the report stays self-contained. Collision meshes are excluded from the render so a collider hull can't obscure the asset (a collider-only file still gets a preview).
- **Summary cards** — total scanned, passed / warnings / failed / import-failed, and **average health**.
- **Export Risk Level** — *Safe to Export* / *Export with Known Issues* / *Export Not Recommended*.
- **Health trend** — an inline sparkline of average health across previous runs.
- **Duplicate-geometry banner** — flags files sharing a mesh signature (catches accidental re-exports before publishing).
- **Per-file cards** — health badge, mesh/tri counts, error/warning counts, and an expandable issue list.
- **Filter / sort / search** — All / Fail / Warning / Pass / Import-failed; sort by health, name, errors or tris.

Alongside the HTML, a `.json` with the same data is written — for tooling, diffing, and the CLI.

---

## Delta scan

With **Delta** on, files unchanged since the last scan (by modification time + size) are skipped and their previous results reused — repeat scans of a large library are near-instant. Edit one file, re-scan, and only that file is re-validated.

---

## What teams use it for

- **Triage** — sort by health, fix the worst offenders first.
- **Catch re-exports** — the duplicate banner spots two files that are really the same mesh.
- **Sign-off** — a pre-publish / pre-merge snapshot of project health.
- **Trend** — watch average health climb run over run.

→ For an automated gate, run the same scan headless in CI: [Headless CLI](cli.md).
→ Full field-by-field structure: [Report Schema](../reference/report-schema.md).
