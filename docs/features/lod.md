# LOD Tools

Generate Level-of-Detail chains, set when each level appears on screen, preview the switching live in the viewport, and validate the result — all from the **LOD Tools** panel in the GameExport sidebar.

---

## One setting: keep %

You set **only** the decimation — how many triangles each LOD keeps (LOD1 50 % / LOD2 25 % / LOD3 10 %). The **switch @ screen %** (when each level takes over) is **derived automatically** from the keep %, so the chain is always valid and descending. The **Reference FOV** is the vertical field of view the switch percentages are authored against.

!!! tip "Manual switch %"
    Need precise control over the switching distances? Tick **Set manually (advanced)** under *Switch @ screen %* to edit LOD1/2/3 @ and Cull by hand. Otherwise the auto value is shown read-only (*Auto from keep %: 50 % / 25 % / 12 %*).

---

## Generating LODs

1. Select a base mesh (LOD0).
2. Set the keep % and the **Levels** count (1–3).
3. Click **Generate LODs**.

This creates `Name_LOD1`, `Name_LOD2`, … beside the base and writes a **switching policy** onto LOD0. Re-running tops up the chain rather than clobbering it. LODs are **always triangulated** (engines triangulate anyway).

The base is **always validated first** — a base mesh with errors won't generate LODs, since decimating a broken mesh just copies the defect into every level. Fix LOD0, then generate.

**Apply / Refresh Policy** (next to the switch values) re-writes the policy onto selected chains *without* regenerating geometry — use it after changing the keep % or FOV. Select the base or any of its LODs first.

---

## Exporting LODs

There is no separate LOD export button — the main **Export Selected** (Export box) handles LODs and groups them per engine automatically:

- **Unity / Godot** — one FBX per asset with the LODs nested as `_LOD0/_LOD1/…`; Unity auto-creates the LOD Group on import.
- **Unreal** — the base FBX (`SM_Asset.fbx`) plus a **separate file per level** (`SM_Asset_LOD1.fbx`, …). Import the base, then add each `_LOD#.fbx` as an LOD level.

Select the asset (the base pulls in its LODs and collider) and click Export Selected. → [Engine Presets](../reference/engine-presets.md).

Background: Unity is the only one of the three that does **not** auto-generate LODs from a mesh, so it needs the decimated meshes you provide. Unreal and Godot can auto-generate, so shipping your own LODs there is optional. Sources: [Unity LOD import](https://docs.unity3d.com/6000.1/Documentation/Manual/importing-lod-meshes.html), [Unreal static-mesh LODs](https://dev.epicgames.com/documentation/en-us/unreal-engine/importing-static-mesh-lods-using-fbx-in-unreal-engine), [Godot mesh LOD](https://docs.godotengine.org/en/stable/tutorials/3d/mesh_lod.html).

---

## Preview (runtime emulator)

The preview shows exactly which level the engine would display at a given camera distance, using the same screen-size math the policy stores.

1. Turn on **Preview LODs**.
2. Drag **Distance (m)** — or click **Live (from viewport)** and orbit / zoom. The Live button is blue while running.
3. The readout shows e.g. `Chair: LOD2 @ 27 m / 14 % screen`.

While **Preview** is on, editing the switch % updates the view **live** — no need to click Apply. Turning preview off restores the original visibility of every level.

→ The full screen-size model is documented in `LOD_SPEC.md` in the addon.

---

## LOD validation

The **Check LOD chains** toggle lives in the **Rules** tab (it's a validation rule, not a generation tool). It validates **any** LOD chain — generated, imported, or hand-made — so it catches problems even in LODs the addon didn't build. When a chain exists, **Run Validation** adds an **LOD** category:

| Check | Severity | Meaning |
|---|---|---|
| **LOD Not Reduced** | error | a level has ≥ as many tris as the previous one (a duplicate that was never decimated) |
| **LOD Lost UV Channel** | error | a level dropped a UV layer present on LOD0 |
| **LOD Material Mismatch** | warning | a level uses different material slots than LOD0 (materials pop on swap) |
| **LOD Bounds Deviation** | warning | a level's bounding box differs too much from the base (silhouette popping) |
| **LOD Naming / Chain Gap** | warning | broken `_LODn` numbering |
| **LOD Threshold Order / Cull** | error / warning | switch % not strictly descending, or cull above the last switch |
| **LOD Detail / Switch Mismatch** | warning | an aggressively decimated level activates while the object is still large on screen |

!!! note "Nanite users"
    UE5 Nanite often makes manual LODs unnecessary. If you don't use LODs, simply don't generate them — the LOD category stays at 100.
