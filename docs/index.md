---
hide:
  - navigation
---

# Game Export Pipeline

**Keep every asset inside your production budget — for Blender teams.** Validate, budget, LOD, batch-scan and export your meshes engine-ready for Unity and Unreal Engine (and Godot), from one tabbed window.

Game Export Pipeline turns the tedious pre-export checklist — scale applied? UVs unwrapped? lightmap UV present? *within the triangle / draw-call / texture-memory budget?* — into a single **Asset Preflight** window with per-asset and project **budgets**, profiles, safe one-click fixes, and a shareable batch report with a producer dashboard. It scales from a solo artist checking one mesh to a 10-person team gating every asset in CI.

[Get started in 5 minutes :material-rocket-launch:](getting-started/quick-start.md){ .md-button .md-button--primary }
[Installation :material-download:](getting-started/installation.md){ .md-button }

---

## What's inside

<div class="grid cards" markdown>

-   :material-view-dashboard: **[Asset Preflight Window](window.md)**

    ---

    One centered, tabbed window — **Presets · Rules · Scope · Scan · Report** — replaces the old panel sprawl. Validate, pick objects, fix, and read the report in one place.

-   :material-chart-box: **[Budgets & Dashboard](features/budgets.md)**

    ---

    Per-asset and whole-project budgets for triangles, draw calls and texture memory, with a **Project Health** dashboard and used/limit/% bars. Over-budget findings come with computed savings.

-   :material-tune-variant: **[Validation Rules (Presets)](features/profiles.md)**

    ---

    The studio defines the ruleset once in an editable `profiles.json` — or in the no-install **[Specification Editor](features/specification-editor.md)** — and the artist imports it. Everyone validates identically, no transcribing from a PDF.

-   :material-decagram: **[LOD Tools](features/lod.md)**

    ---

    Generate LOD chains with per-level decimation and on-screen switch %, preview them live in the viewport, and validate reduction, materials, UVs and switch thresholds.

-   :material-folder-search: **[Batch Scan & HTML Report](features/batch-scan.md)**

    ---

    Point at a folder of exported assets, get a self-contained, shareable HTML report — health scores, risk level, duplicate-geometry detection, and a quality trend. No internet, no dependencies.

-   :material-console: **[Headless CLI (CI Gate)](features/cli.md)**

    ---

    `blender --background --python preflight_cli.py` runs the same scan in CI and exits non-zero on failures, so bad assets never reach `main`.

-   :material-cube-outline: **[Collision & Export](panels/collision-setup.md)**

    ---

    Generate `UCX_`/`UBX_` colliders, recognise a broad set of collider naming conventions, and export with engine-correct axes and scale.

</div>

---

## Two ways to validate

- **In the scene (live).** Select meshes, open **Asset Preflight → Scan → Run Validation**, fix in **Results**. This is the artist's everyday loop.
- **A folder (batch).** Point **Batch Scan** at exported files; each is imported into a throwaway scene, validated, and rolled up into a shareable HTML report. This is the lead's / CI's view.

→ See [Core Concepts](getting-started/concepts.md) for how the two scores (export gate vs scene quality) and profiles fit together.

---

## Why Game Export Pipeline

- **Engine presets baked in.** Unity, Unreal and Godot need different axes and scale. Pick a profile and forget about it.
- **Validation before export.** Geometry, transform, normals, UV, materials, textures, naming, collision and LOD checks catch broken imports before the FBX leaves Blender.
- **Fix without leaving the window.** Every issue has a Select & Focus (jumps to and frames the problem) and a Safe / Destructive fix.
- **Team-ready.** Shared `profiles.json`, shareable HTML reports, and a headless CI gate — built for small studios, not just solo artists.
- **Deterministic & offline.** Reports are content-hashed and reproducible; nothing phones home.

---

## New here?

Start with [Installation](getting-started/installation.md), then the [5-minute Quick Start](getting-started/quick-start.md). Curious how it all fits together? Read [Core Concepts](getting-started/concepts.md). Got a question? Check the [FAQ](faq.md).

---

## License

Game Export Pipeline is released under the **[GNU General Public License v3.0](https://www.gnu.org/licenses/gpl-3.0.txt)** — the same license as Blender itself.
