# Collision Setup

The **Collision Setup** panel generates collision meshes for your render mesh using the naming convention recognised automatically by both Unreal Engine and Unity on import.

---

## Settings

**Decimate Ratio** controls how much the generated collision mesh is simplified. A value of `0.3` reduces the face count to 30% of the original. A value of `0` skips decimation entirely.

Lower values produce simpler, faster collision shapes at the cost of accuracy. For most game props `0.2`–`0.4` gives a good balance.

### Automatic collision behaviour

The collision workflow adapts automatically to the active engine preset — no manual toggle needed:

| Engine | Behaviour on generate |
|---|---|
| **Unity** | Collision mesh is parented as a child of the render mesh — required by Unity for collision to be recognised on import |
| **Unreal Engine** | Render mesh and its collision object are automatically grouped into a collection — matching Unreal's expected scene structure |
| **Godot / Custom** | Collision mesh is created at scene root alongside the render mesh |

---

## Generate

| Button | Prefix | Shape | Best for |
|---|---|---|---|
| **UCX_ Hull** | `UCX_` | Convex hull | Organic shapes, props, rocks, furniture |
| **UBX_ Box** | `UBX_` | Aligned bounding box | Crates, doors, architectural pieces |

Both buttons create a new mesh object named `UCX_<original name>` or `UBX_<original name>`. If a collision mesh with that name already exists it is replaced.

→ How engines interpret these names, and the full set of collider conventions the validator recognises (configurable): [Collider Naming](../reference/collider-naming.md)

---

## Remove All Collision / Dissolve Collections

**Remove All Collision** deletes every `UCX_` and `UBX_` object in the scene.

**Dissolve Collections** removes the auto-generated collections created for Unreal and moves all objects back to the scene root. The objects themselves are not deleted.

!!! warning "Remove All Collision affects the entire scene"
    It is not limited to the current selection — it finds and deletes every object whose name starts with `UCX_` or `UBX_`.
