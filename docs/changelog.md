# Changelog

All notable changes are documented here. The format follows [Keep a Changelog](https://keepachangelog.com/), versioning follows [Semantic Versioning](https://semver.org/).

---

## [1.0.0] — Current

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
