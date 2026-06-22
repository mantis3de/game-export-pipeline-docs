# Individual Steps

The **Individual Steps** panel exposes each preparation operation separately, for when you need more control than the one-click [Quick Reset](quick-reset.md).

---

## Pivot

| Button | What it does |
|---|---|
| **Pivot Bottom** | Moves the origin to the bottom-centre of the bounding box. Use for props, furniture, architecture — anything that sits on a surface. |
| **Pivot Center** | Moves the origin to the geometric centre of the bounding box. Use for objects that rotate around their centre (wheels, doors, weapons). |
| **Set Game Pivot** | Applies the right convention for the chosen **asset type** in one click — Prop / Architecture / Foliage get a bottom-centre origin; Weapon / Vehicle get a bounds-centre origin. Handy when you don't have a tech artist enforcing pivot rules. |

---

## Transforms and modifiers

| Button | What it does |
|---|---|
| **Apply Transforms** | Bakes the current location, rotation, and scale into the mesh data and resets all three to their identity values (`location = 0`, `rotation = 0°`, `scale = 1`). |
| **Apply Modifiers** | Applies all modifiers on the selected objects and removes them from the modifier stack. The mesh is converted to its final evaluated state. |

!!! warning "Apply Modifiers is destructive"
    Once modifiers are applied they cannot be recovered (unless you undo). Run this only when the modifier stack is finalised. The **Export** panel has an **Apply Modifiers** toggle that applies modifiers during export without touching the original stack — prefer that approach if you still need the modifiers for further editing.

---

## Triangulate

**Triangulate** converts all n-gons and quads to triangles using Blender's standard triangulation algorithm. Game engines triangulate meshes at import anyway — doing it explicitly in Blender gives you control over the result and avoids engine-specific triangulation artefacts on curved surfaces.

This is the same operation as adding a **Triangulate** modifier and applying it.

---

## Mesh Data = Object Name

**Mesh Data = Object Name** renames each mesh data-block to match its object's name — object `Barrel_01` gets a mesh data-block named `Barrel_01`. This keeps the Outliner tidy and avoids the mismatched `Cube.001` / `Barrel_01` pairs that some engines and pipeline scripts trip over.

Acts on the **selection**, or the **whole scene** if nothing is selected.

!!! note "Shared meshes are skipped"
    A mesh data-block used by several objects (linked duplicates) is left untouched — renaming it to match one object would be ambiguous. The status line reports how many were renamed and how many shared meshes were skipped.

---

## Add Lightmap UV

**Add Lightmap UV** creates a second UV channel (`UVMap.001` by default) using Blender's **Lightmap Pack** algorithm. The second channel is required by real-time GI and baked lighting in both Unity and Unreal Engine.

!!! note "A second UV channel already exists?"
    If the mesh already has two or more UV channels, this button is a no-op — it will not overwrite or duplicate an existing channel.

If you need more control over the lightmap layout (padding, island scale), use Blender's **UV Editor → Lightmap Pack** directly and skip this button.
