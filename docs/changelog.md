# Changelog

All notable changes are documented here. The format follows [Keep a Changelog](https://keepachangelog.com/), versioning follows [Semantic Versioning](https://semver.org/).

---

## [Unreleased]

---

## [1.0.0] — Current

### Added
- N-Panel UI in the **GameExport** tab (`View3D > Sidebar > GameExport`).
- FBX export with engine presets for **Unity**, **Unreal Engine**, **Godot**, and **Custom** axes.
- **Quick Reset** — pivot to bottom, origin to world zero, apply rotation and scale in one click.
- **Individual Steps** panel — separate buttons for pivot bottom/center, apply transforms, apply modifiers, triangulate, and add lightmap UV.
- **Collision Setup** panel — generate `UCX_` convex hull and `UBX_` bounding box collision meshes; configurable decimate ratio; Colliders as Children mode for Unity.
- **Scene Organization** panel — group render meshes with their collision objects into named collections; batch-export each collection into its own subfolder.
- **Validation** panel — eight checks: scale, UVs, lightmap UV, mesh topology, materials, textures, naming, and collision; configurable poly count and texture size thresholds.
- **Fix** panel — individual fix operators for each validation category plus **Fix All Issues**.
- **Export** panel — Export Active Object and Batch Export Selected; auto-cleanup toggles (Apply Transforms, Apply Modifiers, Triangulate, Collect Textures).
- **Forward Axis gizmo** — viewport arrow showing export forward direction, updates with engine preset.
- **Scale Reference** — 1 m cube and 1.8 m human-height reference box in the viewport.
- Export prefix setting (default `SM_`).
- Relative and absolute export path support.
- **FBX Tools** panel — auto-detects [Better FBX Importer & Exporter](panels/fbx-tools.md) at runtime; no manual toggle required.
- **Better FBX export integration** — when Better FBX is installed, Export Active Object opens its full export dialog (`INVOKE_DEFAULT`); Batch Export uses it silently with engine-matched axis presets.
- **Better FBX import** — Import FBX… button in the FBX Tools panel opens Better FBX's import dialog when the addon is installed.
- **Auto-validation after Fix** — the Validation panel refreshes automatically after every individual fix operator and after Fix All Issues.
- **Relative path guard** — exporting to a `//` path from an unsaved `.blend` file now reports a clear error instead of failing silently with a read-only path.
