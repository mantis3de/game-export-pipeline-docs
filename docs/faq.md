# FAQ

Common questions about Game Export Pipeline. Can't find your answer? Reach out through your store page or the [GitHub repository](https://github.com/Mantis3de).

---

## Installation

??? question "The GameExport tab does not appear after installing the addon."
    Confirm the addon is enabled: open **Edit → Preferences → Add-ons**, search for "Game Asset Export Pipeline", and make sure the checkbox is ticked. If the checkbox is on but the tab is still missing, check the Blender System Console (`Window → Toggle System Console` on Windows, or launch Blender from a terminal on macOS/Linux) for registration errors.

??? question "I installed the extracted folder instead of the zip and now I get an import error."
    Remove the broken install from **Preferences → Add-ons**, then re-zip the `game_export_pipeline_v1.5.1` folder and install the `.zip` file. Blender expects the compressed archive, not the folder itself.

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

## Asset Validator window & profiles

??? question "Where did the Validation and Fix panels go?"
    They're now inside the **Asset Validator** window — click **Open Asset Validator** at the top of the GameExport tab. Validation and results live in **Scan**. The viewport tools (Individual Steps, Collision Setup, LOD Tools, Export) are still in the N-panel — manual fixes are in **Individual Steps**.

??? question "The window closes when I click Scan Folder / Export / Fix."
    That's a Blender limitation: operator buttons in a dialog popup close it after running. Tab switches, picking a profile and **Run Validation** keep it open (they act in place). Just reopen the window with one click — your results are still there. For long issue lists, work in the **docked GameExport sidebar** instead — it has a real scrollbar.

??? question "I changed a threshold but the report still shows the old result."
    The report reflects the **last** validation run. Change settings, then click **Run Validation** again. Also check you edited the right field — e.g. *UV Padding (texels)* in the Rules tab, not *LOD Bounds Tolerance*.

??? question "What's the difference between 'Rules file' and 'Export Report'?"
    **Rules file** is an **input** — the path to a `profiles.json` with your team's rules. **Export Report** is an **output** — it saves the validation *results* to disk. Two different files, two directions. See [Validation Profiles](features/profiles.md).

??? question "How do I make my whole team use the same rules?"
    Commit a `profiles.json` to your project repo, point each artist's addon at it via **Presets → Rules file**, and use the same `--profiles` in CI. Everyone validates identically. See [Validation Profiles](features/profiles.md).

??? question "Score is 29 but my assets look fine — why?"
    There are two scores. **Export Gate** is pooled — one blocking error tanks it. **Scene Quality** is the mean per-asset and stays high if most assets are clean. A low gate + high quality means *one* thing is blocking, not that everything is bad. See [Core Concepts](getting-started/concepts.md).

---

## Batch & CI

??? question "Does the batch scan mess with my open scene?"
    No. Each file is imported into a throwaway scene, validated, and removed. Your open scene is untouched. See [Batch Scan](features/batch-scan.md).

??? question "The batch found no files."
    Only **FBX / glTF / GLB / OBJ** are scanned. Check the folder path and the **Subfolders** toggle in the Scope tab.

??? question "How do I block bad assets in CI automatically?"
    Run `preflight_cli.py` headless — it exits non-zero on failures, so the job fails. See [CI Gate](workflows/ci-gate.md).

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
    Yes. Create the extra hull meshes manually in Blender and name them `UCX_<name>_00`, `UCX_<name>_01`, etc. Unreal Engine merges them into a compound collision shape on import. The addon recognises a broad, configurable set of collider names — see [Collider Naming](reference/collider-naming.md).

---

## Fix

??? question "Will renaming my mesh break its collision/LOD names?"
    No — **Fix Names** (Individual Steps → Naming) renames the **whole asset group** at once: select the render mesh and it pulls in the LODs and colliders from the scene and renames them together (`Rock_01` → `SM_Rock_01`, `UCX_Rock_01` → `UCX_SM_Rock_01`). Validation also matches colliders to meshes regardless of the `SM_` prefix, so a mismatch won't cause a false "no collision" warning.

??? question "Apply Modifiers destroyed my modifier stack."
    The **Apply Modifiers** button (Individual Steps → Transforms & Mesh) is destructive — it applies and removes modifiers from the actual scene objects. Use the **Apply Modifiers** toggle in the **Export** box instead, which applies modifiers only on a temporary export copy and leaves your stack intact.
