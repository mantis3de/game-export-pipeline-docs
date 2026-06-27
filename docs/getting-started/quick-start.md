# Quick Start (5 Minutes)

From a raw mesh to an engine-ready, validated asset — using the **Asset Preflight** window.

!!! tip "Restart after installing"
    Blender caches addon code. After installing or updating, **fully quit and reopen Blender** before testing.

---

## 1. Open the window

1. In the 3D Viewport, press **N** to open the sidebar and select the **GameExport** tab.
2. Click the big **Open Asset Preflight** button at the top.

A centered window opens with five tabs: **Presets · Rules · Scope · Scan · Report**.

---

## 2. Set the rules (Presets / Rules tabs)

1. Got a spec `.json` from a studio (or the [Specification Editor](../features/specification-editor.md))? In **Presets**, click **Import Preset (.json)** — it sets every Rule at once.
2. No preset? Open the **Rules** tab and set thresholds, checks, naming, budgets and engine by hand.

The header shows **Loaded: `<file>.json`** (or *Default (no preset)*). See [Validation Rules](../features/profiles.md).

---

## 3. Validate the scene (Scan tab)

1. Select the mesh(es) you want to check (selecting nothing checks the whole scene).
2. Go to the **Scan** tab and click **Run Validation**.

The window stays open and the **Results** tab fills in.

---

## 4. Read and fix (Scan tab)

After **Run Validation**, the **Scan** tab shows the results in place:

- a **0–100 score** and an export status: `EXPORT READY` / `READY WITH WARNINGS` / `NOT EXPORT READY`,
- per-category scores (geometry, transform, UV, materials, …),
- a grouped, expandable issue list.

For any issue:

- **Select & Focus** jumps to the exact faces/edges and **zooms the viewport** to them (flipped faces light up red).
- Repairs are manual, in the **Individual Steps** panel (Safe Repairs, Fix Names, transforms…); geometry problems are fixed by hand from Select & Focus.

!!! warning "Re-validate after changes"
    The report reflects the **last** run. After changing a threshold or fixing in Edit Mode, click **Run Validation** again.

Repeat until you reach **EXPORT READY**.

---

## 5. Export

Use the **Export** box at the top of the GameExport panel (or your own pipeline) to write the FBX with the correct axes and scale for the active engine. See [Export](../panels/output.md).

---

## Next steps

- Scan a whole **folder** of exports into a shareable report → [Batch Scan & HTML Report](../features/batch-scan.md).
- Generate and preview **LODs** → [LOD Tools](../features/lod.md).
- Share one ruleset across the **team** → [Validation Profiles](../features/profiles.md).
- Gate assets in **CI** → [Headless CLI](../features/cli.md).
- Follow a full real-world run-through → [Workflows](../workflows/scene-asset.md).
