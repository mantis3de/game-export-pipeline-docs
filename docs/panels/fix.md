# Fix

The **Fix** panel provides targeted operators for each validation category, plus a **Fix All Issues** button that runs every applicable fix in the correct order.

---

## Scale & Transforms

**Apply Scale / Rotation** — bakes the current scale and rotation into the mesh data and resets both to identity values. Equivalent to `Ctrl+A → All Transforms` in Blender's 3D Viewport.

**Fix Negative Scale** — detects and corrects negative scale values (caused by mirroring with `Ctrl+M` without applying). Negative scale flips normals inside the engine. This operator applies the scale and then recalculates normals.

---

## Mesh

**Fill Holes** — closes non-manifold edges by filling open boundary loops with new faces. Use this to fix the "non-manifold edge(s)" validation issue.

**Triangulate N-gons** — converts faces with more than 4 vertices (n-gons) to triangles. Quads are left as quads; only n-gons are triangulated.

**Remove Loose Vertices** — deletes vertices that are not connected to any edge or face.

**Remove Zero-Area Faces** — deletes degenerate faces whose calculated area is essentially zero (below `1e-8` m²).

---

## Materials & Naming

**Add Missing Material** — if the object has no material slots, creates and assigns a default material named `M_<ObjectName>`.

**Remove Empty Mat Slots** — removes material slots that have no material assigned. Empty slots cause extra draw calls in some engines.

**Fix Naming / Add Prefix** — renames the object and its mesh data to use the prefix set in the [Output](output.md) panel. For example, with prefix `SM_`, an object named `Rock_01` becomes `SM_Rock_01`. Default Blender names (Cube, Sphere, etc.) are also replaced.

---

## UV

**Add UV Map (Smart Project)** — if the object has no UV maps, creates one using Blender's **Smart UV Project** algorithm. The result is a reasonable starting point but is not a substitute for a hand-unwrapped UV map for hero assets.

**Add Lightmap UV** — adds a second UV channel using **Lightmap Pack**. See [Individual Steps → Add Lightmap UV](individual-steps.md#add-lightmap-uv) for details.

---

## Collision

**Add Missing Collision** — if the object has no `UCX_` or `UBX_` collision mesh, generates a convex hull collision mesh using the same settings as **UCX_ Hull** in the [Collision Setup](collision-setup.md) panel.

---

## Fix All Issues

**Fix All Issues** runs every applicable fix in the correct order:

1. Apply scale and rotation
2. Fix negative scale
3. Fill holes
4. Triangulate n-gons
5. Remove loose vertices and zero-area faces
6. Add missing material and remove empty slots
7. Fix naming / add prefix
8. Add UV map (if missing)
9. Add lightmap UV (if missing)
10. Add missing collision

Each step is skipped if the mesh already passes that check, so Fix All is safe to run on assets that are partially or fully clean.

After all steps complete, **Validation runs automatically** so the panel immediately reflects what remains. Issues that cannot be fixed automatically — a texture genuinely missing from disk, a poly count above the threshold — will still appear there for manual attention.
