# Scene Organization

The **Scene Organization** panel groups render meshes and their collision objects into Blender collections, then lets the batch exporter use those collections as subfolders.

!!! note "Colliders as Children must be off"
    Scene Organization is not available when **Colliders as Children** is enabled in the [Collision Setup](collision-setup.md) panel. Disable it first — the panel will show a reminder if it is on.

---

## Collections

**Organize into Collections** scans the scene and groups each render mesh together with its `UCX_` and `UBX_` collision objects into a collection named after the render mesh. Objects that already belong to a collection are skipped.

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

When **Collection as Catalog (subfolder)** is enabled, the **Batch Export Selected** button in the [Export](export.md) panel creates a subfolder for each collection and places the corresponding FBX inside it.

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
