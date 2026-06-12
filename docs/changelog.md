# Changelog

All notable changes are documented here. The format follows [Keep a Changelog](https://keepachangelog.com/), versioning follows [Semantic Versioning](https://semver.org/).

---

## [Unreleased]

### Added
- **Preview / Isolate** — modal object browser in the Viewport Helpers box. Step through scene meshes with the mouse wheel or arrow keys and isolate each one as **Mesh only**, **Mesh + Collider**, or **Collider only** (press `M` to cycle). `Enter`/`LMB` keeps the current isolation, `Esc`/`RMB` restores everything. A **Restore All** button is provided alongside it.
- **Rename Textures** toggle in the Export auto-cleanup box. When enabled (and Collect Textures is on), copied textures are renamed to the `AssetName_TextureType` convention (e.g. `Rock_01_BaseColor.png`), with the texture type detected from the material node graph.

### Fixed
- **Quick Reset via Fix All mis-placed the first object.** Fix All skipped any object whose origin was already near world zero, so a mesh modelled at the origin kept its centred pivot while the others were reset to bottom-centre. Fix All now resets every eligible object exactly like the standalone Quick Reset.
- **Export left duplicate objects in the scene.** When an object had a collider, the export loop created a working copy that was never deleted — leaving extra render-mesh duplicates in the scene (3 assets appeared to "explode" into 6). The duplicate is now created only when needed and always cleaned up; the FBX output was always correct.
- **Apply Transforms could affect the wrong objects during export.** `transform_apply` runs on all selected objects, so it could bake transforms into the user's original meshes. Transform application now isolates its selection to the temporary export copy only.
- **Objects with colliders skipped cleanup on export.** Apply Transforms and Triangulate ran only on the no-collider export path; objects exported together with a collider now receive the same cleanup, keeping geometry consistent across all assets.
- **Import FBX… and Preview operators were never registered.** Both are now wired into the addon's `register()` so their buttons work.

### Removed
- Unused settings that had no effect: `use_better_fbx` (Better FBX is auto-detected), `use_meshes_subfolder`, and `remove_empties`. Dead imports and a no-op transform call were also removed.

### Changed
- All user-facing strings are now English (the Preview header and reports were partly Polish).

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
- **Batch export progress bar** — Blender's header progress bar shows export progress during batch operations.
- **Export summary popup** — after batch export a dialog shows total exported, warnings, and any failed assets by name.
- **GPL-3.0 license file** included in the package.
- `doc_url` and `tracker_url` set in `bl_info` — links appear directly in Blender's Addon Preferences.
- **Auto collision workflow** — "Colliders as Children" toggle removed; behaviour is now automatic: Unity parents colliders to the render mesh, Unreal organises them into collections, other engines place them at scene root.
- **Scene Organization panel removed** — Dissolve Collections moved to Collision Setup; Subfolders per Collection toggle moved to Export panel.
- **Dissolve Collections** button in Collision Setup reverses collection grouping without deleting any objects.
