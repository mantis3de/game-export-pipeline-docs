# Engine Presets

The **Engine** dropdown in the [Output](../panels/output.md) panel configures the axis orientation and scale factor that Game Export Pipeline applies when exporting FBX files. This page explains what each preset does and why the values differ between engines.

---

## Why axes and scale matter

Blender uses a right-handed coordinate system with **Z as up** and **-Y as forward** by default. The three major game engines each use a different convention:

- **Unity** uses Y-up, with -Z as forward (left-handed).
- **Unreal Engine** uses Z-up, with X as forward (left-handed).
- **Godot** uses Y-up, with -Z as forward (right-handed), mirroring the glTF standard.

If you export an FBX without correcting for these differences, your asset will import rotated 90° or facing the wrong direction. The preset handles this correction invisibly.

---

## Preset values

| Preset | Forward axis | Up axis | Scale | Notes |
|---|---|---|---|---|
| **Unity** | -Z | Y | 1.0 | Matches Unity's default FBX import settings |
| **Unreal Engine** | X | Z | 1.0 | 1 Blender metre = 100 Unreal cm |
| **Godot** | -Z | Y | 1.0 | Compatible with Godot's glTF importer |
| **Custom** | _(user-defined)_ | _(user-defined)_ | _(user-defined)_ | Full manual control |

!!! note "Unreal scale"
    Unreal Engine works in centimetres. 1 metre in Blender equals 100 centimetres in Unreal, so a 1 m cube in Blender arrives as a 100 cm (1 m) cube in Unreal — no scaling correction is needed. The scale value is `1.0` because Blender's FBX exporter already handles the metre/centimetre conversion internally when the axes are set correctly.

---

## Custom preset

When **Custom** is selected, three extra fields appear in the **Engine Scale** panel:

- **Forward** — the Blender axis that maps to the engine's forward direction.
- **Up** — the Blender axis that maps to the engine's up direction.
- **Scale** — a multiplier applied to all geometry on export.

Available axis values: `X`, `-X`, `Y`, `-Y`, `Z`, `-Z`.

Use the Custom preset for engines not covered by the three presets (e.g. CryEngine, O3DE, custom in-house engines), or when a project has non-standard import settings.

---

## Forward Axis gizmo

When **Viewport Helpers → Forward Axis** is enabled, an arrow is drawn in the 3D viewport showing the direction that will be "forward" after export. It updates automatically when the engine preset changes, so you can check your mesh orientation without exporting a test FBX.
