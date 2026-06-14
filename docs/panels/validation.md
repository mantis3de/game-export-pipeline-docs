# Validation

The **Validation** panel scans mesh objects and produces a structured report with per-category scores, inline fix buttons, and an export-readiness gate.

---

## Thresholds and options

Before running, adjust the options in the **Thresholds** box to match your project's requirements:

| Option | Default | Description |
|---|---|---|
| **Max Polygons** | 100,000 | Flag objects with more faces than this |
| **Max Tex Size (px)** | 4096 | Flag textures larger than this dimension |
| **Check UV Overlap** | On | Detect overlapping UV faces. UV0 overlap is a warning (often intentional tiling); UV1 lightmap overlap is an error (breaks baked lighting) |
| **Check UV Padding** | On | Flag lightmap UV islands packed too close together (insufficient texel padding bleeds light at bake time) |
| **Check Material Graph** | On | Validate material node graphs: surface shader connected, single Principled BSDF, standard PBR inputs present |
| **Check Textures** | On | Validate texture color space, broken file references, and texel density relative to poly count |
| **Show Explanations** | On | Show a greyed-out "Impact" and "Fix" hint under each issue explaining what happens in-engine if it is ignored |
| **Marketplace Strict** | Off | Apply Fab / Unreal Marketplace submission rules — missing lightmap UV, non-power-of-two textures, missing collision, and default names become Errors instead of Warnings |

---

## Running validation

**Run Validation** scans all mesh objects in the scene. Three scope buttons next to it narrow the scan:

| Button | Scope |
|---|---|
| **Run Validation** | All mesh objects in the scene |
| **Selection** | Only the currently selected objects |
| **Collection** | Only objects in the active collection |
| **Scene** | Entire scene (same as Run Validation) |

The scan uses pure Python and bmesh — no operator calls. Results appear immediately.

!!! tip "Run validation before every export"
    It takes a few seconds and catches the issues that cause broken imports. Make it part of your export habit.

**Export Report (JSON/CSV)** saves the full issue list to disk in both formats — useful for integrating with pipeline tools or for bug reports.

---

## Validation report

After a run, the panel shows a summary header followed by eight collapsible category sections.

### Summary header

The header shows three things at a glance:

- **Scene Quality** — a 0–100 score averaged across all assets and all categories. A fully clean scene scores 100.
- **Export Gate** — the overall export readiness status and its composite score:
  - `EXPORT READY` — no errors
  - `READY WITH WARNINGS` — warnings only; export is allowed but issues are present
  - `NOT EXPORT READY` — at least one error; fix errors before exporting
- **Error / Warning / Info counts** — total counts across all categories
- **Profile** — the active engine preset and whether Marketplace Strict is on (e.g. `Unreal · Marketplace`)

### Category sections

Each of the eight categories collapses and expands independently:

| Category | What is checked |
|---|---|
| **Geometry** | N-gons, non-manifold edges (holes), zero-area faces, loose vertices, poly count, tris ratio |
| **Transform** | Unapplied scale or rotation, negative scale, non-uniform scale, origin not at world zero, inherited parent scale |
| **Normals** | Inverted or inconsistent face normals |
| **UV** | Missing UV map, UV coords outside 0–1 range, UV overlap (UV0 warning, UV1 error), UV padding on lightmap channel, missing lightmap UV (second channel) |
| **Materials** | Missing or empty material slots, material node graph issues (disconnected shader, multiple Principled BSDF nodes, missing PBR inputs) |
| **Textures** | Missing texture files on disk, non-power-of-two sizes, oversized textures, incorrect color space (Base Color should be sRGB, Normal should be Non-Color), excessive texel density for poly count |
| **Naming** | Default Blender names (Cube, Sphere, etc.), special characters in object name |
| **Collision** | Missing `UCX_` or `UBX_` collision object |

The category header shows the category score (0–100), error count, and warning count. Sections with no issues show a green checkmark.

### Per-issue controls

Each issue in an expanded section has two inline buttons:

- **Select Problem** (crosshair icon) — selects the affected object and, where possible, enters Edit Mode with the problematic vertices, edges, or faces highlighted.
- **Auto-fix** (tool icon) — runs the fix operator for that specific issue. The button turns red when the fix is destructive (changes geometry). Hovering shows the operator name.

When **Show Explanations** is on, a greyed-out block appears under each issue with:

- **Impact** — what happens in-engine if the issue is left unfixed
- **Fix** — what to do (manual or operator-based)
- **Auto-fix label** — whether the one-click fix is safe or destructive

---

## After fixing

The Fix operators (and Fix All) re-run validation automatically after completing. If you fix issues manually in Edit Mode, click **Run Validation** again to refresh the report.

→ Full description of every check: [Validation Checks](../reference/validation-checks.md)
