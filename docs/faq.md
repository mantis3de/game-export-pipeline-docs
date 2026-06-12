# FAQ

Common questions about Game Export Pipeline. Can't find your answer? Reach out through your store page or the [GitHub repository](https://github.com/Mantis3de).

---

## Installation

??? question "The GameExport tab does not appear after installing the addon."
    Confirm the addon is enabled: open **Edit → Preferences → Add-ons**, search for "Game Asset Export Pipeline", and make sure the checkbox is ticked. If the checkbox is on but the tab is still missing, check the Blender System Console (`Window → Toggle System Console` on Windows, or launch Blender from a terminal on macOS/Linux) for registration errors.

??? question "I installed the extracted folder instead of the zip and now I get an import error."
    Remove the broken install from **Preferences → Add-ons**, then re-zip the `game_export_pipeline_v1.0.0` folder and install the `.zip` file. Blender expects the compressed archive, not the folder itself.

??? question "Do I get future updates for free?"
    Yes. Download the latest version from your store account. Check the [Changelog](changelog.md) for what's new.

---

## Export

??? question "My asset imports rotated 90° in Unity / Unreal."
    Make sure the correct **Engine** preset is selected before exporting. Unity needs `-Z` forward / `Y` up; Unreal needs `X` forward / `Z` up. Change the preset and export again — it only takes a moment.

??? question "The export buttons are greyed out."
    The export folder is not set. Open the **Game Export Pipeline** panel at the top of the GameExport tab and enter a valid path in the **Output** field (e.g. `//GameAssets`).

??? question "Batch export only exported some of my objects."
    Batch export runs on the current **selection**. Make sure all the objects you want are selected (Shift+click or `A` to select all) before clicking **Batch Export Selected**.

??? question "My textures are missing from the export folder."
    Confirm **Collect Textures** is enabled in the Export panel. Also check that the textures are not packed into the `.blend` file — packed textures are embedded in the FBX and will not appear as separate files in the `/Textures` folder.

??? question "Can I export glTF instead of FBX?"
    Game Export Pipeline currently exports FBX only. For Godot in particular, glTF is often preferred — use Blender's built-in **File → Export → glTF 2.0** with the axis settings from the [Engine Presets](reference/engine-presets.md) table.

---

## Validation

??? question "Validation shows 'No collision mesh' for every object. Do I need collision on everything?"
    No. The collision check is a **Warning**, not an Error. It is safe to export without collision if the engine will generate its own, or if the asset is a non-interactive background prop. The warning does not block export.

??? question "Validation flags UV coords outside 0–1. My mesh uses tiling textures intentionally."
    Tiling textures intentionally use UVs outside the 0–1 range. As long as you are not baking lightmaps with that UV channel, it is safe to ignore the warning. If you are baking, keep a second dedicated lightmap UV channel inside 0–1.

??? question "The poly count warning fires on a deliberately high-poly asset."
    Raise the **Max Polygons** threshold in the Validation panel to match your project's budget. The setting is saved per-scene in the `.blend` file.

??? question "Validation still shows issues after I ran Fix All."
    Fix All resolves issues that can be corrected automatically. Some issues require manual attention: texture files genuinely missing from disk (relink them in the Image Editor), and poly counts that exceed the threshold (decimate or retopologise). Re-run Validation after Fix All to see what remains.

---

## Collision

??? question "My collision mesh is named correctly but Unreal Engine ignores it."
    The collision mesh name must match the render mesh name exactly (case-sensitive) — if the render mesh is `SM_Rock_01`, the collision must be `UCX_SM_Rock_01`, not `UCX_Rock_01`. Also confirm that **Colliders as Children** is **off** for Unreal exports.

??? question "Unity does not recognise my collision mesh."
    For Unity, the collision mesh must be parented to the render mesh inside the FBX hierarchy. Enable **Colliders as Children** in the Collision Setup panel before generating collision, then re-export.

??? question "Can I have multiple convex hulls for one mesh?"
    Yes. Create the extra hull meshes manually in Blender and name them `UCX_<name>_00`, `UCX_<name>_01`, etc. Unreal Engine merges them into a compound collision shape on import. See [Collision Naming](reference/collision-naming.md).

---

## Fix

??? question "Fix Naming renamed my objects but now my collision meshes are mismatched."
    When the render mesh is renamed (e.g. from `Rock_01` to `SM_Rock_01`), existing collision meshes keep their old names (`UCX_Rock_01`). Delete the old collision meshes and regenerate them after renaming, or rename them manually to match the new render mesh name.

??? question "Apply Modifiers in the Fix panel destroyed my modifier stack."
    The **Apply Modifiers** button in the Fix panel is destructive — it applies and removes modifiers from the actual scene objects. Use the **Apply Modifiers** toggle in the **Export** panel instead, which applies modifiers only on a temporary export copy and leaves your stack intact.
