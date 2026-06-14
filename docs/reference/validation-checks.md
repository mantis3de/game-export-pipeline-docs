# Validation Checks

This page describes every check the **Validation** panel runs, what it looks for, why it matters, and how to fix it.

---

## Transform

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

### Origin not at world zero
**Severity: Warning**

The object's origin (pivot point) is not at world `(0, 0, 0)`. Most engines import the FBX with the pivot at the origin — if the pivot is offset the asset will appear displaced when dropped into a scene.

**Fix:** Use **Quick Reset** (pivot → bottom, origin → world zero) or **Individual Steps → Apply Transforms** after positioning the origin manually.

### Parent has unapplied scale
**Severity: Warning**

The object's parent mesh has an unapplied scale. The child inherits the parent's transform, which compounds the issue.

**Fix:** Apply scale on the parent object first.

---

## Geometry

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

### High triangle ratio
**Severity: Info**

More than the configured percentage of faces are triangles (default 80%). This is informational — triangulated meshes are normal for game assets, but a very high ratio may indicate the mesh was imported pre-triangulated or has been triangulated multiple times.

---

## Normals

### Suspicious / inverted normals
**Severity: Warning**

One or more faces have normals pointing inward (or inconsistently with their neighbours). Inverted normals cause faces to appear transparent or black in the engine, depending on the material and rendering mode.

**Fix:** Use **Fix → Recalculate Normals**, or select the problem faces in Edit Mode and use `Mesh → Normals → Recalculate Outside`.

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

### UV overlap (UV0 / base channel)
**Severity: Warning**

Two or more UV faces in the base UV channel (UV0) overlap each other. Overlap is often intentional for tiling or mirrored surfaces, but it can cause issues with baked ambient occlusion or other per-UV-space bakes.

Disabled when **Check UV Overlap** is off in the Validation thresholds.

### UV overlap (UV1 / lightmap channel)
**Severity: Error**

Two or more UV faces in the lightmap UV channel (UV1) overlap. Overlapping lightmap UVs cause light from one face to bleed onto another during bake, producing incorrect lighting.

**Fix:** Re-pack the lightmap UV channel using Blender's Lightmap Pack (`UV Editor → UV → Lightmap Pack`) or use **Fix → Add Lightmap UV** to regenerate the channel.

Disabled when **Check UV Overlap** is off.

### Missing lightmap UV (2nd UV channel)
**Severity: Warning / Error**

The mesh has only one UV channel. A second channel is required for baked lighting (Unity Lightmapper, Unreal Engine Lightmass / Lumen baking).

Severity depends on the engine preset and Marketplace Strict mode: Warning for Unity (Unity can generate lightmap UVs on import), Error for Unreal Engine and when Marketplace Strict is on.

**Fix:** Use **Fix → Add Lightmap UV** or **Individual Steps → Add Lightmap UV**.

### UV padding too small (lightmap channel)
**Severity: Warning**

Lightmap UV islands are packed closer together than the configured minimum texel padding. At the target lightmap resolution, insufficient margin causes light to bleed between islands during baking.

Adjust the padding requirements in the Validation thresholds (**Lightmap Resolution** and **UV Padding (texels)**). Disabled when **Check UV Padding** is off.

**Fix:** Re-pack the lightmap UV with a larger margin, or increase the lightmap resolution.

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

### Surface shader not connected
**Severity: Warning**

The material has a node graph but the shader output is not connected to the Material Output node. The material will render as a flat default in the engine.

Disabled when **Check Material Graph** is off.

**Fix:** Open the material's Shader Editor and connect the shader node (e.g. Principled BSDF) to the Material Output's Surface input.

### Multiple Principled BSDF nodes
**Severity: Warning**

The material contains more than one Principled BSDF node. Only one is connected to the output — the others are unused and may indicate a setup mistake. Some engine importers only read the first BSDF node they find.

Disabled when **Check Material Graph** is off.

### Missing PBR inputs
**Severity: Info / Warning**

Standard PBR texture inputs are absent from the Principled BSDF: Base Color and Roughness are flagged as warnings; Normal and Metallic are flagged as informational. Having these inputs unconnected is not always a problem, but it is unusual for a complete game-ready material.

Disabled when **Check Material Graph** is off.

---

## Textures

### Missing texture file on disk
**Severity: Error**

A texture node references an image file that does not exist at the stored path. The FBX will export but the texture will be missing in the engine.

**Fix:** Relink the texture in Blender's Image Editor (`Image → Replace`), or pack it into the `.blend` file.

### Non-power-of-two texture
**Severity: Warning**

A texture's width or height is not a power of two (e.g. 512, 1024, 2048, 4096). Non-power-of-two textures cannot be mipmapped correctly in most real-time engines and waste GPU memory.

On Marketplace Strict mode this becomes an Error.

**Fix:** Resize the texture to the nearest power of two in an image editor before re-importing it into Blender.

### Oversized texture
**Severity: Warning**

A texture exceeds the **Max Tex Size** threshold. Large textures increase streaming time and GPU memory usage.

**Fix:** Downscale the texture, or raise the threshold if the size is intentional.

### Incorrect color space
**Severity: Warning**

A texture is assigned the wrong color space for its role. Base Color textures should use **sRGB**; Normal, Roughness, Metallic, and other data textures should use **Non-Color**. Wrong color space causes incorrect lighting or washed-out normal maps in the engine.

Disabled when **Check Textures** is off.

**Fix:** Select the Image Texture node in the Shader Editor and change the Color Space dropdown to the correct value.

### Excessive texel density
**Severity: Info**

The texture resolution is very high relative to the mesh's polygon count — the ratio of `(max texture dimension)² / triangle count` exceeds the **Texels per Triangle Warn** threshold. This usually means either the texture is oversized for the mesh detail, or the mesh is too low-poly to justify the texture budget.

Disabled when **Check Textures** is off.

---

## Naming

### Default Blender name
**Severity: Warning / Error**

The object uses a default Blender name (Cube, Sphere, Cylinder, Plane, etc.). These names carry no meaning in the engine asset browser and make it difficult to identify assets.

Becomes an Error when Marketplace Strict is on.

**Fix:** Rename the object, or use **Fix → Fix Naming / Add Prefix** to apply the configured prefix and rename automatically.

### Special characters in name
**Severity: Warning / Error**

The object name contains characters outside letters, digits, underscores, hyphens, dots, and spaces. Some engines and import pipelines reject or mangle names with special characters.

When **Strict Naming (ASCII)** is enabled, non-ASCII characters (ą, é, ś, etc.) are also flagged. Becomes an Error when Marketplace Strict is on.

**Fix:** Rename the object to use only safe characters, or use **Fix → Fix Naming / Add Prefix** to strip problematic characters automatically.

---

## Collision

### No collision mesh
**Severity: Warning / Error**

No `UCX_<name>` or `UBX_<name>` object exists for this mesh, and no collision child with those prefixes was found. The mesh will import without physics collision unless the engine generates its own.

Becomes an Error when Marketplace Strict is on.

**Fix:** Use **Collision Setup → UCX_ Hull** or **UBX_ Box**, or use **Fix → Add Missing Collision**.

→ [Collision Naming](collision-naming.md)
