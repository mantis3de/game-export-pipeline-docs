# Collision Setup

The **Collision Setup** panel generates collision meshes for your render mesh using the naming convention recognised automatically by both Unreal Engine and Unity on import.

---

## Settings

**Decimate Ratio** controls how much the generated collision mesh is simplified before it is created. A value of `0.3` reduces the face count to 30% of the original. A value of `0` skips decimation entirely (full-resolution collision).

Lower values produce simpler, faster collision shapes at the cost of accuracy. For most game props, a ratio of `0.2`–`0.4` gives a good balance.

**Colliders as Children (Unity)** changes how the collision mesh is parented after creation. When enabled, the collision object is parented to the render mesh — which is the convention Unity requires for collision to be recognised on import. When disabled, the collision object is created as a sibling at the scene root — which is the Unreal Engine convention.

!!! tip "Unreal or Unity?"
    Leave this off for Unreal Engine exports. Turn it on when your engine preset is Unity.

---

## Generate

| Button | Prefix | Shape | Best for |
|---|---|---|---|
| **UCX_ Hull** | `UCX_` | Convex hull | Organic shapes, props, rocks, furniture |
| **UBX_ Box** | `UBX_` | Aligned bounding box | Crates, doors, architectural pieces, anything boxy |

Both buttons create a new mesh object and name it `UCX_<original name>` or `UBX_<original name>`. If the object already has a collision mesh with that name, it is replaced.

→ How engines interpret these names: [Collision Naming](../reference/collision-naming.md)

---

## Remove All Collision

**Remove All Collision** deletes every `UCX_` and `UBX_` object in the scene. Use it to clean up and regenerate if the collision meshes are no longer needed or need to be rebuilt.

!!! warning "This removes all collision in the entire scene"
    Remove All Collision is not limited to the current selection — it finds and deletes every object whose name starts with `UCX_` or `UBX_`. Make sure that is what you want before clicking.
