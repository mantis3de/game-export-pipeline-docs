# Individual Steps

The **Individual Steps** panel is the single place for manual, one-by-one preparation and repair operations. It's grouped into labelled boxes so related actions sit together. (The old *Quick Reset* and *Fix* panels were folded into this one.)

---

## Prepare

**Pivot + Origin + Apply Scale** runs the three steps most artists do before every export, in order:

1. **Pivot to bottom** — origin to the bottom-centre of the bounding box, so the mesh sits on the ground plane in-engine.
2. **Origin to world zero** — moves the object so its origin sits at `(0, 0, 0)`; geometry moves with it (no visual change).
3. **Apply rotation + scale** — bakes them so `rotation = 0°`, `scale = 1`.

!!! warning "This moves objects in the scene"
    Step 2 translates the object to world zero. Run it on a copy or a dedicated export collection, not on a laid-out scene.

---

## Naming

**Fix Names (SM_ / LOD / UCX_UBX)** renames the selected asset to engine-correct names in one click — manual, never automatic:

- render mesh → `SM_<Name>`
- LODs → `SM_<Name>_LOD#`
- colliders → `UCX_`/`UBX_<Name>` (keeps the kind, follows the mesh's final name, indexes multi-hull as `_01/_02`)

You only need to select the render mesh — Fix Names pulls in its LODs and colliders from the scene by name and renames the whole asset group together.

---

## Pivot / Origin

| Button | What it does |
|---|---|
| **Pivot Bottom** | Origin to the bottom-centre of the bounding box (props, furniture, anything that sits on a surface). |
| **Pivot Center** | Origin to the geometric centre (wheels, doors, weapons that rotate around their centre). |
| **Set Game Pivot** | Applies the pivot convention for the chosen **pivot preset** (Prop / Architecture / Weapon / Vehicle…) in one click. |

---

## Transforms & Mesh

| Button | What it does |
|---|---|
| **Apply Transforms** | Bakes location, rotation and scale into the mesh data and resets all three to identity. |
| **Apply Modifiers** | Applies and removes all modifiers (destructive — prefer the Export panel's *Apply Modifiers* toggle if you still need them). |
| **Triangulate** | Converts n-gons/quads to triangles, with control over the result. |
| **Mesh Data = Object Name** | Renames each mesh data-block to match its object (shared/linked meshes are skipped). |

---

## UV

| Button | What it does |
|---|---|
| **Add Lightmap UV** | Creates a second UV channel (Lightmap Pack) for baked GI. No-op if two channels already exist. |
| **Add UV Map (Smart Project)** | Generates a base UV channel via Smart UV Project for meshes with none. |

---

## Safe Repairs

Non-destructive, undoable fixes — apply the ones you need by hand:

| Button | What it does |
|---|---|
| **Recalculate Normals** | Recomputes normals facing outside (fixes flipped faces). |
| **Fix Negative Scale** | Applies mirrored (negative) scale and flips normals back. |
| **Remove Empty Mat Slots** | Removes empty material slots. |

Geometry repairs (holes, n-gons, loose/zero-area faces) stay manual: use **Select & Focus** in the results, then Blender's own tools.

---

## Cleanup

| Button | What it does |
|---|---|
| **Remove All Collision** | Deletes every collider in the scene (any configured naming). |
| **Remove All LODs** | Deletes generated LOD children (`_LOD1+`); the base stays. |
| **Dissolve Collections** | Flattens collections back to the scene root. |
