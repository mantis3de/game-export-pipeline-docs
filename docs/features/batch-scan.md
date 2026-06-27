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
- **Thumbnails** — a rendered preview of each asset (toggle in Scope, or `--no-thumbnails` in the CLI). Each asset is **isolated** during render (other objects hidden) so the preview shows only that asset, and collision meshes are excluded so a collider hull can't obscure it. Pick **Small / Medium / Large** (Thumbnail Size in Scope) — it changes both render resolution and display size. Embedded as base64, so the report stays self-contained.
- **Summary cards** — total scanned, passed / warnings / failed / import-failed, **ignored**, and **average health**.
- **By rule** — every actionable finding grouped by rule with a count (*Missing Texture File — 6 assets ▸*), collapsible, sorted worst-first. The fastest way to see which rule trips most often. Purely informational findings (INFO) are excluded so it stays a list of real problems.
- **Export Risk Level** — *Safe to Export* / *Export with Known Issues* / *Export Not Recommended*.
- **Health trend** — an inline sparkline of average health across previous runs.
- **Duplicate-geometry banner** — flags files sharing a mesh signature (catches accidental re-exports before publishing).
- **Per-file cards** — health badge, mesh/tri counts, error/warning counts, a ▸ chevron, and the issue list. Files **with errors or warnings (or a failed import) are expanded by default** so the reader sees the problems without clicking; clean files stay collapsed. Findings you marked intentional show a muted **ignored** tag.
- **Filter / sort / search** — All / Fail / Warning / Pass / Import-failed / **Ignored** (shows only the muted findings); sort by health, name, errors or tris; **Expand all / Collapse all**.

Alongside the HTML, a `.json` with the same data is written — for tooling, diffing, and the CLI.

---

## History & Compare

Every folder scan (and every **Export JSON** from a scene) drops a **timestamped** copy into a `history/` subfolder of the report destination (`2026-06-21_22-58-32_…_report.json`), one per run — nothing is overwritten. The history list in the **Report** tab loads them automatically; each row's 🔗 icon renders that snapshot's HTML on demand.

Tick **2 or more** snapshots and press **Compare Selected** for a self-contained **Quality Diff** (oldest vs newest):

- a one-number **`%` improvement** + headline cards — avg health `base → target`, open issues `base → target`, **Fixed**, **New**, **Ignored (muted)**, assets improved / regressed,
- **Most improved** and **Worst regressions** assets (with score deltas),
- the **Fixed**, **New**, and **Ignored** finding lists (asset — rule).

**Fixed** means an actionable finding resolved entirely; **Ignored** means muted-but-still-present (muting never counts as a fix). Comparison runs on the deterministic JSON, not the HTML.

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
