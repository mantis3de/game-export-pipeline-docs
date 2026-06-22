# Export

!!! note "Merged into the top box"
    The standalone **Export** panel was removed. Everything below — the **FBX / glTF** target, the **Export Selected** button and the **Auto-cleanup** toggles — now lives in the single **[Export](output.md)** box at the top of the GameExport tab, next to the folder, engine and prefix. The behaviour is unchanged; it's just in one place now.

---

## Engine label

The top of the panel shows the active engine and its axis settings:

- **Unity — Y-up, -Z fwd**
- **Unreal — Z-up, X fwd**
- **Godot — Y-up, -Z fwd**
- **Custom axes** (when Custom preset is selected)

This is a read-only reminder. To change it, go back to the [Output](output.md) panel at the top of the GameExport tab.

---

## Format — FBX or glTF/GLB

A **Format** switch selects the output:

- **FBX** (default) — Autodesk FBX, the engine-agnostic choice for Unity, Unreal, and general pipelines. Uses the engine preset's axis settings and, when installed, [Better FBX](fbx-tools.md).
- **glTF/GLB** — the open glTF 2.0 standard, preferred by Godot and web. glTF is a standardized Y-up format, so it imports with consistent orientation everywhere; the only choice is the **container**:
    - **GLB (single file)** — mesh + textures packed into one self-contained `.glb` (recommended).
    - **Separate (.gltf)** — a `.gltf` + `.bin` + loose texture files.
- **Export Custom Properties** (glTF only) — include object custom properties as glTF `extras` (engine-specific).

Both formats honour the same engine preset, the same auto-cleanup toggles, and the same budget / release gate. Tangents follow the engine convention automatically — baked for Unity/Unreal, computed on import for Godot.

!!! note "glTF exports the render mesh"
    Collision conventions differ per engine in glTF (Godot, for example, uses name suffixes like `-col`), so the FBX `UCX_`/`UBX_` auto-include does not apply to glTF yet — glTF exports the render mesh. FBX collision auto-inclusion is unchanged.

---

## Export folder warning

If the export folder is not set, the panel shows an alert box and an inline path field. Set the folder here or in the [Output](output.md) panel — the export button does not appear until a valid path is entered.

---

## Export Selected

**Export Selected** adapts its behaviour to how many objects are selected and whether [Better FBX](fbx-tools.md) is installed:

| Situation | Behaviour |
|---|---|
| **One object selected + Better FBX installed** | Opens Better FBX's full export dialog. All Better FBX settings — FBX version, axis preset, smoothing groups, tangents, and more — are available there. |
| **Multiple objects selected** | Silent batch export — one FBX per object, no dialog. |
| **Better FBX not installed** | Silent export using Blender's built-in FBX exporter, regardless of how many objects are selected. |

In all cases, any `UCX_` and `UBX_` collision meshes with a matching name are included in the same FBX as their render mesh automatically — you do not need to select them.

When exporting multiple objects, a **progress bar** appears in the Blender header while export is running. When all objects are processed, a **summary popup** appears showing how many assets were exported, any warnings, and a list of failed objects if applicable.

!!! note "Collision meshes are included automatically"
    You do not need to select collision meshes separately. The exporter finds `UCX_<name>` and `UBX_<name>` objects and includes them in the same FBX as the render mesh.

!!! warning "Relative export path requires a saved .blend file"
    If your export path starts with `//`, Blender resolves it relative to the saved `.blend` file. If the file has not been saved yet, export will fail with a path error. Either save the file first or set an absolute path.

---

## Auto-cleanup on Export

These toggles control what happens to the mesh during export. They operate on a temporary copy — the original scene objects are not modified.

| Toggle | Default | What it does |
|---|---|---|
| **Apply Transforms** | On | Bakes location, rotation, and scale before export |
| **Apply Modifiers** | On | Evaluates the modifier stack before export |
| **Triangulate** | On | Converts quads and n-gons to triangles |
| **Collect Textures** | On | Copies used textures to a `/Textures` subfolder next to the FBX |
| **Rename Textures** | Off | Renames the copied textures to `AssetName_TextureType` (only available when Collect Textures is on) |

!!! tip "Leave Apply Transforms on"
    Most engines expect transforms to be applied. Turning this off can cause assets to import at the wrong position, scale, or orientation.

!!! info "Collect Textures copies, not moves"
    Original texture files in the `.blend` project are not touched. The copies in `/Textures` are what go to the engine.

!!! info "Rename Textures uses the material node graph"
    When enabled, each texture's type (BaseColor, Normal, Roughness, Metallic, and so on) is detected by following its connections in the material's shader nodes, falling back to filename keywords. A texture used by asset `Rock_01` becomes e.g. `Rock_01_Normal.png` in the `/Textures` folder.
