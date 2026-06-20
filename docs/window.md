# The Asset Preflight Window

Everything to do with validation now lives in one place: a centered, tabbed window opened from the **Open Asset Preflight** button at the top of the **GameExport** sidebar.

It replaces the old scattered *Validation* and *Fix* panels. The interactive viewport tools (Collision, LOD, Quick Reset, Export, FBX) stay in the N-panel — see [Viewport Tools](panels/output.md).

!!! tip "Docked or floating — your choice"
    The same tabbed UI is **docked in the N-panel** (persistent — it never closes, and its buttons don't dismiss anything) and also opens as a **centred window** via *Open as window*. Use the panel for continuous work; pop the window when you want more room.

---

## How the window behaves

The window is a wide centered dialog with a tab bar across the top:

**Presets · Scope · Rules · Scan · Results**

- **Switching tabs** and **Run Validation** act *in place* — the window stays open.
- **Operator buttons** (Apply Profile, Scan Folder, Export Report, Fix) run their action and then **close the window** — reopen it with one click. This is a Blender limitation for dialog popups, not a bug.

!!! info "Why a popup and not a dockable editor?"
    Blender's Python API can't create a free-floating, dockable application window. A centered dialog with a tab bar is the closest equivalent, and it's the same proven mechanism the addon already used for the validation window.

---

## Presets tab — what ruleset to use

- **Profile** dropdown + **Apply Profile** — load an engine/studio ruleset (thresholds, checks, severities, naming, colliders). **Reload** re-reads `profiles.json` after you edit it.
- **Rules file** — point at a project `profiles.json` to share rules across the team. *(This is the rules **input**, not the report output.)*
- **Asset Type** — *PBR / Lightmap / Procedural / Kitbash-Atlas* — relaxes the findings that are normal for that kind of asset (e.g. Kitbash treats UV overlap & near-zero UV area as info).
- **Engine** / **Strict Naming** / **Marketplace Strict** — quick policy toggles.
- **Naming Template** — studio convention (`SM_{Category}_{Name}_{NN}`) + one-click rename.
- **Collider names** — the prefixes/suffixes treated as collision meshes. Add your studio's convention here.

→ [Validation Profiles](features/profiles.md) · [Naming Templates](features/naming.md) · [Collider Naming](reference/collider-naming.md)

---

## Scope tab — what to validate

- **In-scene scope** — validation runs on **selected** meshes, or the **whole scene** if nothing is selected. The object list previews what will be checked.
- **Batch folder** — set a folder of exported files plus the report output path, and the **Subfolders** / **Delta** toggles. (Run it from the Scan tab.)

→ [Batch Scan & HTML Report](features/batch-scan.md)

---

## Rules tab — thresholds & checks

Expand **Settings** to tune the ruleset:

| Control | Purpose |
|---|---|
| **Max Polygons** | Triangle budget before HIGH_POLY fires |
| **Max Tex Size (px)** | Oversized-texture threshold |
| **Check UV Overlap / Padding / Materials / Textures** | Toggle whole check groups |
| **UV Padding (texels) / Lightmap Res (px)** | The exact padding rule (shown when UV Padding is on) — these are the `4px @ 1024px` in the warning |
| **Flag Default Names / Marketplace Strict** | Naming policy |
| **Show Explanations / Frame View on Focus / Auto-open** | Report UX |
| **Asset Budgets** | Per-asset + whole-scene triangle / material / VRAM budgets (the dashboard) |
| **LOD checks / Bounds Tolerance** | LOD validation toggles |

!!! warning
    Changes apply on the **next Run Validation** — they don't retro-update the current report.

→ [Validation Checks](reference/validation-checks.md)

---

## Scan tab — run it

- **Run Validation** — validate the scene (in place; window stays open). Shows the active profile.
- **Scan Folder** / **Open HTML Report** — batch-scan the folder set in Scope and open the shareable report.
- **Export Report (.json / .csv)** — save the current scene results to disk. *(This is the results **output**, not the rules.)*

---

## Scan & Results tab

Run validation and read the results in one place (no tab-hopping). Shown after a run:

- **Project Health dashboard** — the level's totals vs the project budget as used / limit / % bars, plus **Top by triangles**. See [Budgets & Dashboard](features/budgets.md).
- **Summary** — export gate score + status, scene quality, error/warning/info counts, per-category scores.
- **Objects to fix** — a checklist of validated objects (tick which to repair); **All** / **Errors only** quick-selects.
- **Fix Safe Issues** — every *deterministic* fix on the ticked objects (apply scale/rotation, names, materials, lightmap UV). Destructive geometry "repairs" are intentionally **not** auto-applied — guessing at topology can quietly make a mesh worse.
- **Issue list** — grouped by object or category. Each issue has:
    - **Select & Focus** — selects the exact components, opens the right editor (UV / Shader / Material tab) and **frames the viewport** on the problem. Flipped faces enable the orientation overlay. Geometry problems are fixed by hand from here.
    - **Fix This** — that issue's safe fix (shown only for deterministic fixes).
    - **Ignore** — mark a finding intentional, on this object (ghost) or every mesh in the scene (globe). Downgraded to an `(ignored)` info note; **Un-ignore** restores it.
    - **Impact / Fix** hints (when *Show Explanations* is on).

→ [Validate a Scene Asset](workflows/scene-asset.md) for the full loop.
