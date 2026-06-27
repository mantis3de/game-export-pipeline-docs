# The Asset Preflight Window

Everything to do with validation now lives in one place: a centered, tabbed window opened from the **Open Asset Preflight** button at the top of the **GameExport** sidebar, or from the always-visible **Preflight** button in the 3D viewport header (top bar). The header button stays put through any operation and turns **red** when the last scan found errors, so the gate status is visible from anywhere.

It replaces the old scattered *Validation* and *Fix* panels. The interactive viewport tools (Individual Steps, Collision, LOD, Export) stay in the N-panel — see [Viewport Tools](panels/output.md).

!!! tip "Docked or floating — your choice"
    The same tabbed UI is **docked in the N-panel** (persistent — it never closes, and its buttons don't dismiss anything) and also opens as a **centred window** via *Open as window*. Use the panel for continuous work; pop the window when you want more room.

---

## How the window behaves

The window is a wide centered dialog with a tab bar across the top:

**Presets · Rules · Scope · Scan · Report**

- **Switching tabs** and **Run Validation** (scene mode) act *in place* — the window stays open.
- **Operator buttons** (Scan Folder, Export JSON/HTML, Import Preset) run their action and then **close the window** — reopen it with one click. This is a Blender limitation for dialog popups, not a bug.

!!! info "Why a popup and not a dockable editor?"
    Blender's Python API can't create a free-floating, dockable application window. A centered dialog with a tab bar is the closest equivalent, and it's the same proven mechanism the addon already used for the validation window.

---

## Presets tab — load / save the ruleset

The Presets tab is **only** for loading and saving the ruleset — every individual rule lives in the **Rules** tab.

- **Import Preset (.json)** — load a spec file (from a studio, or the [Specification Editor](features/specification-editor.md)). It sets **every** Rule to match the file immediately — there is no dropdown and no Apply step.
- **Loaded: `<file>.json`** — shows which spec is driving the rules (or **Default (no preset)** when none is loaded or the file is missing).
- **Save Current Rules as .json…** — write your current Rules into a `profiles.json` to share with the team.

There are **no built-in presets** — you import one or build your own in Rules.

→ [Validation Rules (Presets)](features/profiles.md) · [Specification Editor](features/specification-editor.md)

---

## Scope tab — what to validate

- **In-scene scope** — validation runs on **selected** meshes, or the **whole scene** if nothing is selected. The object list previews what will be checked, with an **All** toggle to tick / untick everything and a *Ticked: X/Y* count.
- **List ↔ viewport sync** — clicking a row (the highlight, not the tick) selects that object in the viewport and makes it active; selecting an object in the viewport / Outliner highlights its row. It works both ways.
- **Batch folder** — set a folder of exported files plus the **Subfolders** / **Delta** toggles in the Scan tab. *(The report destination is set in the **Report** tab.)*

→ [Batch Scan & HTML Report](features/batch-scan.md)

---

## Rules tab — thresholds & checks

Tune the whole ruleset here — this is where the freelancer dials everything in to match the studio's spec. It now also holds the **Naming** (strict ASCII, template + category/start index) and **Collider names**, which used to live under Presets.

| Control | Purpose |
|---|---|
| **Naming / Naming Template / Collider names** | Strict ASCII, the studio convention (`SM_{Category}_{Name}_{NN}` + category & start index), and the prefixes/suffixes treated as colliders. |
| **Max Polygons / Max Tex Size (px)** | HIGH_POLY and oversized-texture thresholds |
| **Category masters** | One toggle per category — **Geometry, Normals/Orientation, Transforms, Collision, Naming, UV, UV Overlap, UV Padding, Materials, Textures, Texel Density**. Untick a master to skip that whole category. |
| **▸ per-category arrow** | Each master expands into its **individual findings** — 56 granular toggles in total. |
| **UV Padding (texels) / Lightmap Res (px)** | The exact padding rule (shown when UV Padding is on) |
| **Flag Default Names** | Naming policy |
| **Limits & Budgets** | Per-asset + whole-scene triangle / material / VRAM budgets, plus **Max Vertices** and **Max Object Count** (opt-in). |
| **Studio rules** | Opt-in checks: **Hidden Objects Forbidden, Packed Images Forbidden, Unique Material Names**. |
| **LOD checks / Bounds Tolerance** | LOD validation — validates any LOD chain (generated, imported or hand-made). |

!!! tip "Granular = precise profiles"
    Every finding can be turned off individually, so you can build a very precise ruleset and **Save Current as Profile…** — the whole set (masters + the 56 sub-toggles) is written into the profile JSON and restored when you apply it. See [Validation Profiles](features/profiles.md).

!!! info "Toggle vs severity"
    A **check toggle** decides whether a finding RUNS (off = dropped entirely). To keep a finding visible but harmless (e.g. an atlas asset where UV overlap is fine), use a **severity override** in the preset to downgrade it to INFO instead of unticking it. The [Specification Editor](features/specification-editor.md) exposes those overrides, and after import they're listed read-only in the Rules tab (*Severity overrides (from preset)*).

!!! warning
    Changes apply on the **next Run Validation** — they don't retro-update the current report.

→ [Validation Checks](reference/validation-checks.md)

---

## Scan tab — run it & read the results

The Scan tab is the **action + on-screen results** (no tab-hopping). A **Scene / Folder** toggle at the top picks what you validate.

- **Run Validation** — **scene mode** validates in place (the window stays open) and shows the results below; **folder mode** scans the folder set in Scope and writes its report. *Run Validation only validates — it does not save a report. Saving/exporting is the **Report** tab's job.*
- **Big scenes show progress** — above ~200 objects, Run Validation (and Export) run in batches with a **progress bar in the viewport header** (`Validating 120/400 (30%) — Esc to cancel`) so Blender stays responsive instead of freezing. Press **Esc** to cancel.
- After a scene run you get:
    - **Project Health dashboard** — the level's totals vs the project budget as used / limit / % bars, plus **Top by triangles**. See [Budgets & Dashboard](features/budgets.md).
    - **Summary** — export gate score + status, scene quality, error/warning/info counts, per-category scores.
    - **Objects to fix** — a checklist of validated objects (tick which to repair); **All** / **Errors only** quick-selects. **Clicking a row** (the blue highlight, not the tick) also **selects that object in the viewport** and makes it active.
- **Re-check** — re-runs validation in place; use it after fixing something by hand instead of scrolling back up. There is **no bulk auto-fix** — repairs are manual, in the [Individual Steps](panels/individual-steps.md) panel (Safe Repairs, Fix Names, transforms…). Geometry problems are fixed from **Select & Focus**.
- **Issue list** — **By Object** or **By Category** (a toggle at the top). In By-Category there's an **Expand all / Collapse all** switch, and in the floating window each section header has a **▲** that jumps back to the top (a popup can't scroll, so it reopens fresh at the top). Each issue has:
    - **Select & Focus** — selects the exact components, opens the right editor (UV / Shader / Material tab) and **frames the viewport** on the problem. Flipped faces enable the orientation overlay. Geometry problems are fixed by hand from here.
    - **↻ Re-check** — re-validate in place, right where you fixed it.
    - **Ignore** — mark a finding intentional, on this object (ghost) or every mesh in the scene (globe). Downgraded to an `(ignored)` info note (it stops affecting the asset score) but stays visible as an audit trail. *Ignored findings still count as **present** in Compare — muting never reads as a fix.*

    - **Impact / Fix** hints (when *Show Explanations* is on).
- **Ignored** — a **Show / Hide** toggle previews exactly what's muted (scoped to the ticked objects); each row has a **✕** to un-ignore that single finding, plus **Un-ignore All**.

→ [Validate a Scene Asset](workflows/scene-asset.md) for the full loop.

---

## Report tab — save, export & compare

The Report tab owns the report **artefacts** for the active mode (Scene or Folder).

- **Report destination** — *Scene* mode writes to the **Report folder** you set here; *Folder* mode writes to its **Output**. **There is no implicit fallback** — if no Scene Report folder is set, nothing is saved and the panel shows a red *"Set a Report folder"* warning. **Open Report Folder** reveals the location.
- **Thumbnails (in HTML report)** — render a textured preview per asset. Thumbnails are produced **only for the HTML export** (Run Validation never renders them). Size: Small / Medium / Large.
- **Export JSON** — builds the report and writes a **dated snapshot** into `history/` (JSON only); it adds an entry to the history list below. Use this to record a point you can compare later.
- **Export HTML** — builds and opens the shareable, self-contained HTML report (with thumbnails when enabled). HTML is for viewing/sharing and **does not** add a history entry.
- **Report history** — the dated JSON snapshots for this mode. Pointing at a folder auto-loads them. Each row has a 🔗 icon that renders that snapshot's HTML on demand and opens it.
- **Compare Selected** — tick **2 or more** snapshots → a **Quality Diff** (oldest vs newest): one-number `%` improvement, Avg health / Open issues deltas, **Fixed / New / Ignored** lists, and most-improved / worst assets. *Fixed = resolved entirely · Ignored = muted but still present.*

→ [Batch Scan & HTML Report](features/batch-scan.md)
