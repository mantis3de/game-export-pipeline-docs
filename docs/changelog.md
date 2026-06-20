# Changelog

All notable changes are documented here. The format follows [Keep a Changelog](https://keepachangelog.com/), versioning follows [Semantic Versioning](https://semver.org/).

---

## [Unreleased] — Asset Preflight

A major workflow update turning the panel sprawl into one tabbed window and adding
team / pipeline tooling.

### Budgets & dashboard (Budget Manager)
- **Asset budgets** — per-asset triangle / material (draw-call) / texture-VRAM limits, a **Budget** category + score, and **computed optimization savings** ("downscale 2 textures → save ~48 MB").
- **Project Health dashboard** — whole-scene totals vs project budget as used / limit / % bars + **Top expensive** assets, in the window and the HTML report.
- **Executive Summary** hero + **thumbnails** in the HTML report.
- **Project presets** — Mobile Low/High, PC Indie, VR.

### Release gate & regressions
- **Release Gate** — a one-glance **RELEASE READY / BLOCKED** verdict (in the dashboard, HTML report, and CLI), blocked by failing assets, import failures, project-budget overruns, or average health below the **Release Health Threshold** (default 85, set per profile).
- **Regression tracking** — a "Since last scan" block comparing project health and triangle / VRAM / material totals against the previous report, plus the assets whose score dropped the most.

### Authoring & validation
- **Texel density check** (opt-in) — flags assets whose texture pixels-per-metre fall outside a target band, for consistent density across a set.
- **Lightmap UV outside 0–1** — errors when the lightmap channel packs outside the 0–1 square (breaks baking).
- **Set Game Pivot** — one-click origin presets per asset type (Prop / Architecture / Foliage → bottom-center; Weapon / Vehicle → bounds-center).
- **LOD decimate options** — preserve symmetry (with axis) and triangulate LODs in the generator.

### More
- **glTF / GLB export** — Export Selected now offers **FBX or glTF 2.0** (GLB single-file or separate `.gltf`), for Godot and web pipelines. Same engine preset, cleanup, and budget gate; tangents per engine convention.
- **Material / Texture checks+** — flags a **non-standard surface shader** (custom node group that won't bake to PBR) and **mismatched texture resolutions** within one material (both informational).
- **Backup before destructive fix** — optional snapshot of affected meshes into a hidden `GEP_Backup` collection before triangulate / fill-holes / loose-vert / zero-area repairs.
- **Viewport header button** — an always-visible **Preflight** button in the 3D viewport header that opens the window (turns red when the last scan found errors).
- **Naming templates** + one-click auto-rename (`SM_{Category}_{Name}_{NN}`).
- **Per-object / whole-scene Ignore** (mark intentional → `(ignored)` info).
- **Asset Type** layer (PBR / Lightmap / Procedural / Kitbash-Atlas).
- **Safe-only fixes** — destructive geometry auto-repair removed in favour of Select & Focus.
- **Persistent tabbed N-panel** (docked; buttons don't dismiss it) alongside the window.
- **CI package** (`ci/` — GitHub Action + pre-commit) + shareable rule-packs.

### Asset Preflight window
- One centered, tabbed window — **Presets · Scope · Rules · Scan · Results** — opened from a single **Open Asset Preflight** button. Replaces the old *Validation* and *Fix* panels. Interactive viewport tools (Collision, LOD, Quick Reset, Export, FBX) stay in the N-panel.

### Validation Profiles
- Editable, shareable [`profiles.json`](features/profiles.md) — engine/studio rule bundles (thresholds, checks, naming, collider conventions, per-issue severity overrides). Built-ins: **Unity Mobile/Desktop, Unreal, Godot 4, Marketplace**.
- Point at a **project** `profiles.json` so a whole team validates identically; **Apply** / **Reload** in the Presets tab.

### LOD tools
- **LOD generator** with per-level decimation keep-% and on-screen switch-%, written as a switching policy onto LOD0.
- **Live preview** — drive the visible level from a distance slider or the real viewport camera.
- **LOD validation** — reduction monotonicity, material / UV consistency, bounds deviation, naming/chain gaps, and switch-threshold order.

### Batch scan & HTML report
- Scan a **folder** of exported assets (FBX/glTF/GLB/OBJ) into a self-contained, shareable [HTML report](features/batch-scan.md): health scores, export-risk level, **duplicate-geometry** detection, and a quality trend. **Delta scan** skips unchanged files.

### Headless CLI
- [`preflight_cli.py`](features/cli.md) runs the same scan via `blender --background` and exits non-zero on failures — a drop-in **CI gate**.

### Collider recognition
- Broad, **configurable** [collider naming](reference/collider-naming.md) (Unreal `UCX_/UBX_/USP_/UCP_`, `*.collision`, `*_col`, Godot `-colonly`, …), matched to the render mesh — no more false errors on imported colliders.

### Select & Focus
- Now **frames the 3D viewport** on the selected problem and enables the face-orientation overlay for flipped faces.

### Report schema
- Scene report bumped to **1.1** (additive: `scores.lod`, profile/collider config keys); batch report `batch-1.0`. See [Report Schema](reference/report-schema.md).

---

## [1.0.0]

First public release.

### Export
- FBX export with engine presets for **Unity**, **Unreal Engine**, **Godot**, and **Custom** axes.
- One FBX per selected mesh; any matching `UCX_`/`UBX_` collision meshes are included automatically.
- Auto-cleanup toggles applied to a temporary copy: **Apply Transforms**, **Apply Modifiers**, **Triangulate**, **Collect Textures**, and **Rename Textures** (`AssetName_TextureType`, detected from the material node graph).
- Export prefix setting (default `SM_`) and both relative (`//`) and absolute export paths, with a clear error when a relative path is used from an unsaved `.blend`.
- Header progress bar during batch export and a summary popup showing exported, warnings, and any failed assets.

### Better FBX integration
- Auto-detects [Better FBX Importer & Exporter](panels/fbx-tools.md) at runtime — no manual toggle. When installed, a single-object export opens its full dialog and batch export uses it silently with engine-matched axis presets.
- **Import FBX…** button opens Better FBX's import dialog when the addon is installed.

### Geometry prep
- **Quick Reset** — pivot to bottom, origin to world zero, apply rotation and scale in one click.
- **Individual Steps** — pivot bottom/center, apply transforms, apply modifiers, triangulate, add lightmap UV.
- **Preview / Isolate** — modal browser that steps through scene meshes (mouse wheel / arrow keys) and isolates each as **Mesh only**, **Mesh + Collider**, or **Collider only** (`M` to cycle), plus a **Restore All** button.

### Collision
- Generate `UCX_` convex hull and `UBX_` bounding box collision meshes with a configurable decimate ratio.
- Automatic collider workflow by engine: **Unity** parents colliders to the render mesh, **Unreal** organises them into collections, other engines place them at scene root.
- **Dissolve Collections** reverses collection grouping without deleting any objects.

### Validation
- Asset validation across transform, geometry, normals, UV (incl. **overlap** and **padding**),
  materials, textures, naming and collision. Runs on the **evaluated** mesh when export applies modifiers.
- Dual-metric scoring: pooled **export gate** (`EXPORT READY` / blocking) plus **scene quality**
  (mean per-asset, for ranking / batch triage); per-category and per-asset scores.
- Engine + **Marketplace** severity presets.
- **Report UI**: collapsible per-category sections, impact explanations, fix-confidence
  (Safe / Destructive / Manual), and **Select Problem** with shader-graph & UV-editor deep-link.
- **Asset Report export** (JSON / CSV): versioned schema 1.0, deterministic content hash.
- **Batch validation**: Selection / Collection / Scene.
- Auto-fixes, including a non-destructive **Fix Safe Issues**.

### Fix
- An individual fix operator for each validation category plus **Fix All Issues**; the Validation panel refreshes automatically after every fix.

### Viewport helpers
- **Forward Axis gizmo** — viewport arrow showing the export forward direction, updates with the engine preset.
- **Scale Reference** — 1 m cube and 1.8 m human-height reference box.

### Packaging
- N-Panel UI in the **GameExport** tab (`View3D > Sidebar > GameExport`).
- GPL-3.0 license included; `doc_url` and `tracker_url` set in `bl_info` so links appear in Blender's Addon Preferences.
