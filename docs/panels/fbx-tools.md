# FBX Tools

The **FBX Tools** panel integrates with the [Better FBX Importer & Exporter](https://blendermarket.com/products/better-fbx-importer--exporter) addon when it is installed alongside Game Export Pipeline. It is a paid third-party addon sold separately on Blender Market.

---

## Status indicator

The top of the panel always shows the current state:

| State | Message |
|---|---|
| Installed | **Better FBX detected — active** — export goes through Better FBX automatically |
| Not installed | **Better FBX not installed — using built-in** — export falls back to Blender's built-in FBX exporter |

No manual toggle is needed. Game Export Pipeline detects the addon at export time and routes accordingly.

---

## Import

When Better FBX is installed the **Import FBX…** button opens Better FBX's full import dialog with all of its settings — bone orientation, normals, smoothing, scale, animation, and more.

If Better FBX is not installed the button is greyed out. Use Blender's built-in **File → Import → FBX** instead.

---

## Export behaviour

### With Better FBX installed

Clicking **Export Active Object** opens Better FBX's own export dialog. All of Better FBX's settings are available there — FBX version, axis preset, smoothing groups, tangents, raw normals, material style, vertex colour, animation, and more.

Batch Export (from the [Export](export.md) panel) runs silently using Better FBX with sensible defaults matched to the active engine preset:

| Engine preset | Better FBX axis |
|---|---|
| Unity | Unity |
| Unreal Engine | Unreal1 |
| Godot | OpenGL |
| Custom | MayaZUp |

### Without Better FBX

Export works exactly as before — Blender's built-in FBX exporter is used with the axis and scale settings from the active engine preset.

---

!!! info "Why Better FBX?"
    Better FBX uses Autodesk's native FBX SDK under the hood. This produces FBX files with more accurate smoothing groups, better tangent data, and broader compatibility with game engines compared to Blender's built-in exporter.

!!! warning "Save your .blend file before exporting"
    If your export path starts with `//` (relative), Blender resolves it relative to the saved `.blend` file. Exporting from an unsaved file will fail with a read-only path error. Save the file first, or set an absolute export path.
