# Collider Naming

A collision mesh should **not** be validated like a render mesh — colliders are routinely non-manifold, have no UVs and no material. The addon recognises a collider by its name and then:

- **skips** it as a render asset (no false non-manifold / missing-UV / no-material errors), and
- counts it as "collision present" for its render mesh (no false *missing collision* warning).

Recognition is **configurable** in **Asset Preflight → Presets → Collider names**, and overridable per profile. Matching is **case-insensitive** and tolerant of Blender's `.001` duplicate suffix.

---

## Recognised by default

### Prefixes (start of the name)

| Prefix | Origin |
|---|---|
| `UCX_` | Unreal — convex hull |
| `UBX_` | Unreal — box |
| `USP_` | Unreal — sphere |
| `UCP_` | Unreal — capsule |
| `MCDCX_` | merged convex (some UE pipelines) |
| `COL_`, `COLLISION_`, `COLLIDER_`, `COLMESH_` | generic / studio |
| `CL_`, `PHYS_`, `PHYSICS_`, `HITBOX_` | generic / studio |

### Suffixes (end of the name)

| Suffix | Origin |
|---|---|
| `_collision`, `.collision`, `-collision` | generic / studio |
| `_collider`, `.collider`, `-collider` | generic |
| `_collisionmesh`, `_collmesh`, `_colmesh`, `_collision_mesh` | generic |
| `_col`, `-col`, `.col` | generic |
| `_colonly`, `-colonly`, `_convcol`, `-convcol`, `_convcolonly`, `-convcolonly` | Godot suffixes |
| `_phys`, `_physics`, `_physmesh` | generic |
| `_hitbox`, `_hit`, `_trigger` | generic |
| `_ucx`, `_ubx` | generic |

---

## Matching a collider to its render mesh

The base (render-mesh) name is recovered like this:

- **Prefix convention (Unreal):** `UCX_Barrel_01` → `Barrel` — the trailing hull index is dropped, so `UCX_Barrel` and `UCX_Barrel_01` both pair with `Barrel`.
- **Suffix convention (studio):** `Barrel_01.collision` → `Barrel_01` — the mesh's own numbering is kept; the suffix is the only marker.

This is why an imported asset whose colliders are named `Barrel_01.collision` validates correctly even though it doesn't use the Unreal `UCX_` convention.

---

## Customising

- **In the window:** Presets → *Collider names* → edit **Prefixes** / **Suffixes** (comma-separated). Leave empty to use the defaults above.
- **In a profile:** add a `naming.collider_prefixes` / `naming.collider_suffixes` array to a profile in `profiles.json` so the whole team shares it:

```jsonc
"naming": {
  "strict": true,
  "collider_suffixes": ["_col", ".collision", "_ucx"]
}
```

→ Generate engine-standard colliders with [Collision Setup](../panels/collision-setup.md).
