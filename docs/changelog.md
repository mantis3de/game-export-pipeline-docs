# Changelog

## 1.5.1 — First release

The first public release of **Game Asset Export Pipeline** for Blender 4.2+ — a
validation, fix, LOD, collision and export toolkit for Unity, Unreal Engine and
Godot. Everything below ships in this initial version; there is no prior history.

**Validation & reporting**

- One **Asset Validator** window (docked panel or pop-out) with tabs for Presets, Rules, Scope, Scan and Report.
- 56 individual checks across Geometry, Normals/Orientation, Transforms, UV, UV Overlap, UV Padding (lightmap), Materials, Textures, Texel Density, Naming, Collision, LOD and Budgets — each toggleable, with per-issue severity overrides.
- Studio rules: Max Vertices, Max Object Count, Hidden Objects, Packed Images, Unique Material Names.
- **Select & Focus** jumps to the exact geometry/UV/material behind a finding; per-object and scene-wide **Ignore**; **Isolate** mode for browsing one object at a time.
- Self-contained **HTML report** (offline, no telemetry) with per-object findings, LOD triangle breakdown and a **Quality Diff** to compare snapshots.

**Pipeline tooling**

- Spec-driven rules: a studio authors a `profiles.json` in the standalone **Specification Editor** (offline HTML), the artist **imports** it so every rule matches.
- **Scene Cleanup** (engine-correct rename, per-asset collections, collider un-parenting), **Fix Names**, pivot/origin/transform helpers, and safe repairs.
- **LOD tools** — generate chains from a single keep-% (+ Detail slider), automatic switch distances, list-driven preview with live tri counts.
- **Collision** generation (UCX_ hull / UBX_ box).
- Engine-aware **export** (Unity/Godot nested, Unreal separate `_LOD#`), texture collection, glTF/FBX.
- Headless **CLI** preflight gate for CI.
