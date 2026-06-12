# Validation Checks

This page describes every check the **Validation** panel runs, what it looks for, why it matters, and how to fix it.

---

## Scale

### Unapplied scale
**Severity: Warning**

The object's scale is not `(1, 1, 1)`. Engines read scale values differently than Blender — an unapplied scale of `(2, 2, 2)` may import at the wrong size or break physics colliders.

**Fix:** Use **Fix → Apply Scale / Rotation**, or `Ctrl+A → Scale` in the viewport.

### Unapplied rotation
**Severity: Warning**

The object's rotation is not `(0°, 0°, 0°)`. Similar to scale — some engines apply rotation on import, others do not.

**Fix:** Use **Fix → Apply Scale / Rotation**, or `Ctrl+A → Rotation` in the viewport.

### Negative scale
**Severity: Error**

One or more scale axes are negative (common after mirroring with `Ctrl+M`). Negative scale flips normals, causing faces to appear inside-out in the engine.

**Fix:** Use **Fix → Fix Negative Scale**.

### Non-uniform scale
**Severity: Warning**

The X, Y, and Z scale values are not equal. This can distort physics collision shapes and cause unexpected behaviour with normal maps.

**Fix:** Use **Fix → Apply Scale / Rotation**.

### Parent has unapplied scale
**Severity: Warning**

The object's parent mesh has an unapplied scale. The child inherits the parent's transform, which compounds the issue.

**Fix:** Apply scale on the parent object first.

---

## UV

### No UV maps
**Severity: Error**

The mesh has no UV channels at all. Textures cannot be mapped without at least one UV channel.

**Fix:** Use **Fix → Add UV Map (Smart Project)**, or unwrap manually in the UV Editor.

### UV coords outside 0–1 range
**Severity: Warning**

Some UV coordinates fall outside the standard 0–1 UV tile. This is normal for UDIM or tiling textures, but it can cause incorrect lightmap baking.

**Fix:** Re-unwrap the mesh so all UVs are within the 0–1 tile, or confirm that your engine and lightmap baker support UDIM.

---

## Lightmap UV

### Missing lightmap UV (2nd UV channel)
**Severity: Warning**

The mesh has only one UV channel. A second channel is required for baked lighting (Unity Lightmapper, Unreal Engine Lightmass / Lumen baking).

**Fix:** Use **Fix → Add Lightmap UV** or **Individual Steps → Add Lightmap UV**.

---

## Mesh

### N-gons
**Severity: Warning**

The mesh contains faces with more than 4 vertices. N-gons tessellate unpredictably in different engines and can produce shading artefacts.

**Fix:** Use **Fix → Triangulate N-gons**.

### Non-manifold edges (holes)
**Severity: Warning**

One or more edges are shared by fewer or more than two faces — indicating open holes or internal geometry. This can cause issues with physics collision and rendering.

**Fix:** Use **Fix → Fill Holes**, or fix manually in Edit Mode with `Mesh → Clean Up → Fill Holes`.

### Zero-area faces
**Severity: Warning**

One or more faces have essentially zero area — degenerate triangles or quads that collapse to a line or point.

**Fix:** Use **Fix → Remove Zero-Area Faces**.

### Loose vertices
**Severity: Warning**

Vertices not connected to any edge. They contribute nothing to the mesh but increase vertex count and can confuse some engine importers.

**Fix:** Use **Fix → Remove Loose Vertices**, or `Mesh → Clean Up → Delete Loose` in Edit Mode.

### High poly count
**Severity: Warning**

The face count exceeds the **Max Polygons** threshold. This is a warning, not an error — the FBX exports fine, but the asset may be too heavy for its intended use.

**Fix:** Decimate or retopologise the mesh, or raise the threshold if the count is intentional.

---

## Materials

### No material slots
**Severity: Warning**

The object has no material assigned. It will import as a grey default material in the engine.

**Fix:** Use **Fix → Add Missing Material**.

### Empty material slots
**Severity: Warning**

One or more slots exist but have no material assigned. Empty slots still produce draw calls in some engines.

**Fix:** Use **Fix → Remove Empty Mat Slots**.

---

## Textures

### Missing texture file on disk
**Severity: Error**

A texture node references an image file that does not exist at the stored path. The FBX will export but the texture will be missing in the engine.

**Fix:** Relink the texture in Blender's Image Editor (`Image → Replace`), or pack it into the `.blend` file.

### Non-power-of-two texture
**Severity: Warning**

A texture's width or height is not a power of two (e.g. 512, 1024, 2048, 4096). Non-power-of-two textures cannot be mipmapped correctly in most real-time engines and waste GPU memory.

**Fix:** Resize the texture to the nearest power of two in an image editor before re-importing it into Blender.

### Oversized texture
**Severity: Warning**

A texture exceeds the **Max Tex Size** threshold. Large textures increase streaming time and GPU memory usage.

**Fix:** Downscale the texture, or raise the threshold if the size is intentional.

---

## Naming

### Default Blender name
**Severity: Warning**

The object uses a default Blender name (Cube, Sphere, Cylinder, Plane, etc.). These names carry no meaning in the engine asset browser and make it difficult to identify assets.

**Fix:** Rename the object, or use **Fix → Fix Naming / Add Prefix** to apply the configured prefix and rename automatically.

### Special characters in name
**Severity: Warning**

The object name contains characters outside letters, digits, underscores, hyphens, dots, and spaces. Some engines and import pipelines reject or mangle names with special characters.

**Fix:** Rename the object to use only safe characters.

---

## Collision

### No collision mesh
**Severity: Warning**

No `UCX_<name>` or `UBX_<name>` object exists for this mesh, and no collision child with those prefixes was found. The mesh will import without physics collision unless the engine generates its own.

**Fix:** Use **Collision Setup → UCX_ Hull** or **UBX_ Box**, or use **Fix → Add Missing Collision**.

→ [Collision Naming](collision-naming.md)
