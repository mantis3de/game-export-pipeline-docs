# Quick Start

## Export Your First Asset in 5 Minutes

This guide takes you from a default Blender cube to a validated, engine-ready FBX — using only the **GameExport** panel. Each step is roughly a minute.

!!! tip "Before you begin"
    Make sure the **GameExport** tab is visible in the N-Panel. If it isn't, see [Installation](installation.md).

---

### Minute 1 — Set your target engine and export folder

1. Press **N** in the 3D Viewport to open the N-Panel and click the **GameExport** tab.
2. In the **Game Export Pipeline** panel at the top, click the folder icon next to the path field and choose where you want the FBX files to land (e.g. `//GameAssets`).
3. Set **Engine** to your target: **Unity**, **Unreal Engine**, or **Godot**.
4. Optionally set a **Prefix** (default `SM_`) — this is prepended to every exported filename.

!!! info "What the engine preset does"
    Each preset applies the correct forward axis, up axis, and scale factor automatically. You never need to remember that Unreal wants `X` forward and `Z` up — just pick the engine. See [Engine Presets](../reference/engine-presets.md) for the full table.

---

### Minute 2 — Run validation

1. Open the **Validation** panel (click to expand it in the N-Panel).
2. Select the object you want to export.
3. Click **Run Validation**.

The panel shows eight green or red indicators — Scale, UV, Lightmap UV, Mesh, Materials, Textures, Naming, and Collision. Any red indicator means there is at least one issue in that category. Issues are listed below the indicators.

!!! tip "Nothing selected?"
    Validation runs on the current selection. If nothing is selected, select your mesh first with **A** (select all) or click the object.

---

### Minute 3 — Fix issues

If validation found problems, open the **Fix** panel. You have two options:

- **Fix individual issues** — use the targeted buttons (e.g. **Apply Scale / Rotation**, **Add UV Map**, **Fix Naming / Add Prefix**) to fix one category at a time.
- **Fix All Issues** — the large button at the bottom of the Fix panel applies every available fix in the correct order.

After fixing, run **Run Validation** again to confirm all indicators are green.

!!! note "Fix All is safe to run on clean assets"
    Fix All skips operations that are not needed — it won't re-apply an already-applied scale, re-triangulate an already-triangulated mesh, or add a UV map if one already exists.

---

### Minute 4 — Generate collision (optional)

If your asset needs physics collision in the engine:

1. Select the render mesh.
2. Open the **Collision Setup** panel.
3. Click **UCX_ Hull** for a convex hull (good for most props) or **UBX_ Box** for a tight bounding box (good for crates and architectural pieces).

The collision mesh is created and named `UCX_YourObject` or `UBX_YourObject` automatically, which both Unity and Unreal Engine recognise on import.

→ Full details: [Collision Setup](../panels/collision-setup.md)

---

### Minute 5 — Export

1. Open the **Export** panel.
2. Confirm the engine label shown at the top of the panel matches your target.
3. Click **Export Active Object** to export the selected mesh, or **Batch Export Selected** to export every selected object as a separate FBX.

The FBX lands in the folder you set in Minute 1. Textures are copied to a `/Textures` subfolder if **Collect Textures** is on.

!!! success "Done"
    Your FBX is engine-ready — correct axes, applied transforms, triangulated, with collision and textures alongside it.

---

## Where to go next

<div class="grid cards" markdown>

-   :material-check-circle: **Understand validation**

    ---

    See the full list of checks and what each one looks for in [Validation Checks](../reference/validation-checks.md).

-   :material-cube-outline: **Collision in depth**

    ---

    Learn the UCX/UBX naming convention and Unity vs Unreal differences in [Collision Naming](../reference/collision-naming.md).

-   :material-folder-multiple: **Batch export a whole scene**

    ---

    Organise objects into collections and export each one to its own subfolder in [Scene Organization](../panels/scene-organization.md).

-   :material-help-circle: **Got questions?**

    ---

    Browse the [FAQ](../faq.md).

</div>
