# Fix

The **Fix** panel provides targeted, **non-destructive** operators for each validation category, plus a **Fix Safe Issues** button that runs the safe fixes at once. Destructive geometry repairs were removed by design — geometry problems are fixed manually via **Select & Focus** and Blender's own (undoable) tools.

---

## Scale & Transforms

**Apply Scale / Rotation** — bakes the current scale and rotation into the mesh data and resets both to identity values. Equivalent to `Ctrl+A → All Transforms` in Blender's 3D Viewport.

**Fix Negative Scale** — detects and corrects negative scale values (caused by mirroring with `Ctrl+M` without applying). Negative scale flips normals inside the engine. This operator applies the scale and then flips faces to restore correct normals when an odd number of axes are negative.

---

## Mesh

**Recalculate Normals** — recalculates all face normals to point outward, making them consistent. Does not change any geometry — only normal orientation is affected. Clears most "suspicious normals" validation warnings.

!!! note "Geometry repairs are manual"
    Filling holes, triangulating n-gons, and removing loose / zero-area geometry are **not** auto-applied — guessing at topology can quietly make a mesh worse. For those issues use **Select & Focus** in the report to jump to the exact components, then fix them with Blender's own tools (`Mesh → Clean Up`, etc.), which are fully undoable.

---

## Materials & Naming

**Add Missing Material** — if the object has no material slots, creates and assigns a default material named after the object.

**Remove Empty Mat Slots** — removes material slots that have no material assigned. Empty slots cause extra draw calls in some engines.

**Fix Naming / Add Prefix** — renames the object and its mesh data to use the prefix set in the [Output](output.md) panel. For example, with prefix `SM_`, an object named `Rock_01` becomes `SM_Rock_01`. Default Blender names (Cube, Sphere, etc.) are also replaced. When **Strict Naming (ASCII)** is enabled in settings, non-ASCII characters are stripped from the name.

---

## UV

**Add UV Map (Smart Project)** — if the object has no UV maps, creates one using Blender's **Smart UV Project** algorithm. The result is a reasonable starting point but is not a substitute for a hand-unwrapped UV map for hero assets.

**Add Lightmap UV** — adds a second UV channel using **Lightmap Pack**. See [Individual Steps → Add Lightmap UV](individual-steps.md#add-lightmap-uv) for details.

---

## Collision

**Add Missing Collision** — if the object has no `UCX_` or `UBX_` collision mesh, generates a convex hull or box collision mesh. A small dialog lets you choose between UCX (convex hull, best for organic shapes) and UBX (box, best for simple props) before generating.

---

## Fix Safe Issues

**Fix Safe Issues** runs only the non-destructive fixes — the ones that cannot corrupt geometry:

- Fix negative (mirrored) scale
- Apply scale and rotation
- Remove empty material slots
- Add missing material
- Fix naming / add prefix
- Recalculate normals

Geometry-changing operations (fill holes, triangulate n-gons, remove loose verts / zero-area faces) are intentionally **not** part of the addon — they're done manually with Blender's own tools after **Select & Focus**. Use Fix Safe when you want a quick cleanup without risking any mesh change.

After the fixes complete, **Validation runs automatically**. Issues that can't be fixed safely — a texture genuinely missing from disk, a poly count above the threshold, geometry topology — remain for manual attention.

!!! warning "Fix All moves objects to world origin"
    Step 1 translates every non-parented object so its pivot lands at `(0, 0, 0)`. If your meshes are part of a layout, run Fix All on a dedicated export copy rather than on the original scene.
