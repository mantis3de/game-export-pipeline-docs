# Output & Engine

The **Game Export Pipeline** panel at the top of the GameExport tab is always visible. It controls the two settings that apply to every operation in the addon: where files land and which engine they target.

---

## Output folder

The path field sets the root folder for all exported FBX files. Click the folder icon to browse, or type a path directly.

| Path format | Meaning |
|---|---|
| `//GameAssets` | Relative to the current `.blend` file (recommended) |
| `/Users/me/Assets` | Absolute path |
| `C:\Users\me\Assets` | Absolute path (Windows) |

!!! tip "Relative paths travel with the project"
    Using `//` keeps the export folder relative to the `.blend` file, so the path stays valid when the project is moved or shared. Absolute paths break when the folder structure changes.

If the export folder is not set (empty or `//`), the **Export** panel shows a warning and disables the export buttons until a path is entered.

---

## Engine preset

The **Engine** dropdown controls the axis orientation and scale factor used by the FBX exporter.

| Preset | Forward | Up | Scale | Notes |
|---|---|---|---|---|
| **Unity** | -Z | Y | 1.0 | Unity standard |
| **Unreal Engine** | X | Z | 1.0 | 1 Blender metre = 100 cm in UE |
| **Godot** | -Z | Y | 1.0 | glTF-friendly |
| **Custom** | _(manual)_ | _(manual)_ | _(manual)_ | Set axes and scale manually |

Selecting a preset also updates the **Forward Axis gizmo** in the viewport (if it is enabled) so you can see the exported forward direction while modelling.

→ Full axis and scale table: [Engine Presets](../reference/engine-presets.md)

---

## Prefix

The **Prefix** field is prepended to every exported filename. The default is `SM_` (Static Mesh), which is the convention used by both Unreal Engine and many Unity pipelines.

For example, a mesh named `Rock_01` with prefix `SM_` exports as `SM_Rock_01.fbx`.

Leave the field empty to export without a prefix. The **Fix Naming / Add Prefix** operator in the [Fix](fix.md) panel renames objects in the scene to match the prefix you set here.

---

## Viewport helpers

Two toggles in the **Viewport Helpers** box control non-destructive overlays drawn in the 3D viewport.

**Forward Axis** draws an arrow showing the direction that will be "forward" in the target engine after export. It updates automatically when you change the engine preset. Use it to align your mesh correctly without guessing.

**Scale Ref** draws two reference boxes — a 1×1×1 metre cube and a 1.8 m tall human-height box — so you can check your asset's scale against real-world proportions at a glance.

Both overlays are display-only and have no effect on the exported file.
