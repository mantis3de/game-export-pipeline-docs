# Engine Presets

The **Engine** dropdown in the [Export](../panels/output.md) box configures the FBX axis orientation, the scale factor, and the **LOD/collider file structure** used at export. This page explains what each preset does and why the values differ.

!!! info "Engine is export-only"
    The engine setting affects **export only** — axes, scale, and how LODs/colliders are packed into FBX. It does **not** change validation, naming, or collider matching, and the scene layout is identical regardless of engine. Switching engine never silently flips a rule.

---

## Why axes and scale matter

Blender uses a right-handed system with **Z up** and **-Y forward**. The engines differ:

- **Unity** — Y-up, -Z forward (left-handed).
- **Unreal Engine** — Z-up, X forward (left-handed).
- **Godot** — Y-up, -Z forward (right-handed), mirroring glTF.

Export without correcting and the asset imports rotated or facing the wrong way. The preset handles this invisibly.

## Preset values

| Preset | Forward | Up | Scale | Notes |
|---|---|---|---|---|
| **Unity** | -Z | Y | 1.0 | Unity's default FBX import |
| **Unreal Engine** | X | Z | 1.0 | 1 Blender m = 100 Unreal cm |
| **Godot** | -Z | Y | 1.0 | glTF-friendly |

*(The old "Custom" preset was removed — the three engines cover the supported targets.)*

---

## Export structure per engine

The engine also decides how a multi-part asset (render mesh + LODs + collider) is written:

| | Colliders | LODs |
|---|---|---|
| **Unity / Godot** | nested as a child in **one FBX** | nested in the same FBX as `_LOD0/_LOD1…` (Unity auto-builds the LOD Group) |
| **Unreal** | sibling in the base FBX, matched by `UCX_`/`UBX_` name | **separate file per level**: `SM_Asset.fbx`, `SM_Asset_LOD1.fbx`, … |

In the scene, colliders and LODs are always plain siblings in collections — the nesting/splitting happens only at export.

---

## Forward Axis gizmo

The **Viewport Helpers** box has separate **Forward Axis: Unity / Unreal** buttons that draw an arrow showing the exported forward direction for that engine (Unity −Y, Unreal +X), so you can check orientation without exporting. Because the gizmo is now an explicit per-engine choice, it no longer follows the export Engine setting.
