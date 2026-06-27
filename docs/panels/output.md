# Export (Output & Engine)

The **Export** box at the top of the GameExport tab is always visible and is the single place for everything export-related: the output **Folder**, the **Engine** preset, the filename **Prefix**, the per-asset / textures **subfolder** toggles, the **FBX / glTF** target, the **Export Selected** button, and the **Auto-cleanup on Export** toggles.

---

## Output folder

The path field sets the root folder for exports. Click the folder icon to browse, or type a path.

| Path format | Meaning |
|---|---|
| `//GameAssets` | Relative to the current `.blend` (recommended) |
| `/Users/me/Assets` | Absolute (macOS/Linux) |
| `C:\Users\me\Assets` | Absolute (Windows) |

!!! tip "Relative paths travel with the project"
    `//` keeps the folder relative to the `.blend`, so it stays valid when the project is moved or shared.

If the folder is empty or `//`, the box shows a warning and disables export until a path is entered.

---

## Engine preset

The **Engine** dropdown controls the FBX axes/scale **and** how an asset's LODs and collider are written to disk. It is **export-only** — it does not affect validation or naming.

| Preset | Forward | Up | Scale |
|---|---|---|---|
| **Unity** | -Z | Y | 1.0 |
| **Unreal Engine** | X | Z | 1.0 (1 m = 100 cm) |
| **Godot** | -Z | Y | 1.0 |

**Export Selected** is asset-group aware: it folds an asset's `_LOD#` children and its collider into the asset, then writes them per engine — Unity/Godot get **one nested FBX**, Unreal gets the **base + separate `_LOD#.fbx`** files. → [Engine Presets](../reference/engine-presets.md).

---

## Prefix and folders

- **Prefix** — prepended to every exported filename, the **same for all engines** (export naming is engine-agnostic). Default `SM_`; clear it for none. Already-prefixed object names (e.g. after **Fix Names** in [Individual Steps](individual-steps.md#naming)) are not doubled.
- **Subfolder per asset** — write each asset into its own subfolder (named after its collection). Turn **off** when you already point at the asset's own folder, to avoid a folder-in-folder.
- **Textures in /Textures subfolder** — when *Collect Textures* is on, put textures in a `Textures/` subfolder, or copy them next to the files.

---

## Format & auto-cleanup

Choose **FBX** or **glTF/GLB**. **Auto-cleanup on Export** applies, on temporary copies, the toggled steps (apply transforms / modifiers, triangulate, collect & optionally rename textures) so your scene is never modified.

---

## Viewport helpers

The **Viewport Helpers** box draws non-destructive overlays:

- **Forward Axis: Unity / Unreal** — two buttons; each draws an arrow for that engine's forward direction (Unity −Y, Unreal +X). A ✕ hides it. The choice is explicit per engine, independent of the export Engine setting.
- **Scale Ref: Show** — a 1 m cube + a 1.8 m human-height box to check proportions (identical for Unity and Unreal). A ✕ hides it.

Both are display-only and never touch the exported file.

---

## Preview / Isolate

The **Preview / Isolate** button walks through the scene's mesh objects one at a time, hiding everything else. Colliders (`UCX_`/`UBX_`) are shown as part of the mesh they belong to, never as their own entries.

| Control | Action |
|---|---|
| Mouse wheel · `↑` / `↓` | Previous / next mesh |
| `M` | Cycle isolation mode |
| `Enter` · `LMB` | Keep isolation and exit |
| `Esc` · `RMB` | Restore all and exit |

| Mode | Shows |
|---|---|
| **Mesh only** | Just the render mesh |
| **Mesh + Collider** | The mesh and its colliders |
| **Collider only** | Just the colliders (check hull shape) |

**Restore All** un-hides every object. Preview only toggles visibility — it never moves, deletes, or modifies geometry.
