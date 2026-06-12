---
hide:
  - navigation
---

# Game Export Pipeline

**A Blender addon that takes your mesh from scene to engine-ready FBX in one click — with built-in validation, auto-fix, collision generation, and presets for Unity, Unreal Engine, and Godot.**

Game Export Pipeline lives in the **N-Panel → GameExport** tab. It replaces the tedious pre-export checklist — scale applied? UVs unwrapped? Collision mesh named correctly? Textures power-of-two? — with a single validation run and an optional one-button fix, then exports with the correct axes and scale for your target engine.

[Get started in 5 minutes :material-rocket-launch:](getting-started/quick-start.md){ .md-button .md-button--primary }
[Installation :material-download:](getting-started/installation.md){ .md-button }

---

## What's inside

<div class="grid cards" markdown>

-   :material-export: **[Export](panels/export.md)**

    ---

    Export the active object or batch-export a selection with engine-correct axes and scale. Auto-applies transforms, modifiers, and triangulation on the fly.

-   :material-check-circle: **[Validation](panels/validation.md)**

    ---

    Run eight checks in one click — scale, UVs, lightmap UV, mesh topology, materials, textures, naming, and collision. Results appear as green/red indicators directly in the panel.

-   :material-auto-fix: **[Fix](panels/fix.md)**

    ---

    Fix individual issues or hit **Fix All** to apply scale, repair mesh topology, fill missing UVs, add materials, rename objects, and add collision in a single pass.

-   :material-cube-outline: **[Collision Setup](panels/collision-setup.md)**

    ---

    Generate `UCX_` convex hull or `UBX_` box collision meshes in one click. Supports both Unreal (separate objects) and Unity (child objects) naming conventions.

-   :material-folder-multiple: **[Scene Organization](panels/scene-organization.md)**

    ---

    Group render meshes and their colliders into named collections, then batch-export each collection into its own subfolder.

-   :material-reload: **[Quick Reset](panels/quick-reset.md)**

    ---

    Pivot to bottom, origin to world zero, apply rotation and scale — the three steps you run before every export, done in one button.

</div>

---

## Why Game Export Pipeline

- **Engine presets baked in.** Unity, Unreal Engine, and Godot each need different axis orientations and scale factors. Select your engine and forget about it.
- **Validation before export.** Eight checks catch the issues that cause broken imports — missing UVs, unapplied scale, non-power-of-two textures, missing collision — before the FBX leaves Blender.
- **Fix without leaving the panel.** Every validation issue has a corresponding Fix operator. Run individual fixes or hit **Fix All** to resolve everything in one shot.
- **Collision that engines understand.** Generates `UCX_` and `UBX_` meshes using the Unreal/Unity naming convention so collision imports automatically alongside the render mesh.
- **Viewport helpers.** A live forward-axis arrow and a 1m scale reference box keep you oriented without leaving Blender.

---

## New here?

Start with [Installation](getting-started/installation.md), then follow the [5-minute Quick Start](getting-started/quick-start.md) to export your first asset. Have a question? Check the [FAQ](faq.md).
