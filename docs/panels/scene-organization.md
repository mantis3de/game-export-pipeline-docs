# Scene Organization

The **Scene Organization** panel groups render meshes and their collision objects into Blender collections, then lets the batch exporter use those collections as subfolders.

The collision workflow adapts automatically to the active engine preset — no manual toggle needed:

| Engine | Collision behaviour |
|---|---|
| **Unity** | Collision meshes are parented as children of the render mesh |
| **Unreal Engine** | Render mesh and its collision objects are grouped into a named collection automatically on generate |
| **Godot / Custom** | Collision meshes are created at scene root alongside the render mesh |

The **Organize into Collections** and **Dissolve** buttons are most useful for Unreal Engine workflows, or when you want explicit collection-based subfolder export regardless of engine.

---

## Collections

**Organize into Collections** scans the scene and groups each render mesh together with its `UCX_` and `UBX_` collision objects into a collection named after the render mesh. Objects that already belong to the correct collection are skipped.

Before:
```
Scene Collection
├── Rock_01
├── UCX_Rock_01
├── Barrel_02
└── UCX_Barrel_02
```

After:
```
Scene Collection
├── Rock_01 [collection]
│   ├── Rock_01
│   └── UCX_Rock_01
└── Barrel_02 [collection]
    ├── Barrel_02
    └── UCX_Barrel_02
```

**Dissolve** reverses the operation — it removes the collections and moves all objects back into the root scene collection. The objects themselves are not deleted.

---

## Batch Export with subfolders

When **Collection as Catalog (subfolder)** is enabled, the **Export Selected** button in the [Export](export.md) panel creates a subfolder for each collection and places the corresponding FBX inside it.

For example, with collections `Rock_01` and `Barrel_02` and an export path of `//GameAssets`:

```
GameAssets/
├── Rock_01/
│   └── SM_Rock_01.fbx
└── Barrel_02/
    └── SM_Barrel_02.fbx
```

With the option off, all FBX files land flat in the root export folder.

!!! tip "Useful for large scenes"
    The subfolder approach mirrors the typical folder structure inside Unity (`Assets/Art/Props/`) and Unreal Engine (`Content/Art/Props/`), making it straightforward to drop the exported folder directly into the engine project.
