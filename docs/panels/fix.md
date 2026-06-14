# Fix

The **Fix** panel provides targeted operators for each validation category, plus **Fix Safe Issues** and **Fix All Issues** buttons that run multiple fixes at once.

---

## Scale & Transforms

**Apply Scale / Rotation** — bakes the current scale and rotation into the mesh data and resets both to identity values. Equivalent to `Ctrl+A → All Transforms` in Blender's 3D Viewport.

**Fix Negative Scale** — detects and corrects negative scale values (caused by mirroring with `Ctrl+M` without applying). Negative scale flips normals inside the engine. This operator applies the scale and then flips faces to restore correct normals when an odd number of axes are negative.

---

## Mesh

**Fill Holes** — closes non-manifold edges by filling open boundary loops with new faces. Use this to fix the "non-manifold edge(s)" validation issue.

**Triangulate N-gons** — converts faces with more than 4 vertices (n-gons) to triangles. Quads are left as quads; only n-gons are triangulated.

**Remove Loose Vertices** — deletes vertices that are not connected to any edge or face.

**Remove Zero-Area Faces** — deletes degenerate faces whose calculated area is essentially zero (below `1e-8` m²).

**Recalculate Normals** — recalculates all face normals to point outward, making them consistent. Does not change any geometry — only normal orientation is affected. Clears most "suspicious normals" validation warnings.

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

Destructive operations (fill holes, triangulate n-gons, remove loose verts/zero-area faces, UV generation, collision, lightmap) are intentionally excluded. Use this when you want a quick cleanup without risking mesh changes.

After the fixes complete, **Validation runs automatically**.

---

## Fix All Issues

**Fix All Issues** runs every applicable fix in the correct order:

1. **Position fix** — pivot to bottom-centre, move origin to world `(0, 0, 0)`, apply rotation and scale. Skipped for objects that are parented to another object.
2. Fix negative scale
3. Apply scale and rotation
4. Fill holes
5. Remove loose vertices
6. Remove zero-area faces
7. Triangulate n-gons
8. Remove empty material slots
9. Add missing material
10. Fix naming / add prefix
11. Add UV map (if missing)
12. Add missing collision (if missing — asks for UCX or UBX type first)
13. Add lightmap UV (if missing — opens Lightmap Pack dialog)

Each step is skipped if the mesh already passes that check, so Fix All is safe to run on assets that are partially or fully clean.

After all steps complete, **Validation runs automatically** so the panel immediately reflects what remains. Issues that cannot be fixed automatically — a texture genuinely missing from disk, a poly count above the threshold — will still appear there for manual attention.

!!! warning "Fix All moves objects to world origin"
    Step 1 translates every non-parented object so its pivot lands at `(0, 0, 0)`. If your meshes are part of a layout, run Fix All on a dedicated export copy rather than on the original scene.
