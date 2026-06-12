# Quick Reset

The **Quick Reset** panel runs three preparation steps in a single click — the exact three steps most artists do manually before every export.

---

## Pivot + Origin + Apply Scale

The **Pivot + Origin + Apply Scale** button does the following in order:

1. **Pivot to bottom** — moves the object's origin to the lowest point of the bounding box, centred on X and Y. This makes the mesh sit on the ground plane when dropped into a scene in Unity or Unreal Engine.
2. **Origin to world zero** — moves the object so its origin sits at `(0, 0, 0)` in world space. The mesh geometry moves with it; no visual change in the scene, but the FBX will import with its pivot at the origin.
3. **Apply rotation and scale** — bakes the current rotation and scale values into the mesh data so that `rotation = (0°, 0°, 0°)` and `scale = (1, 1, 1)` in object mode. Engines that read the raw transform values (especially Unity) require this before import.

!!! warning "This moves objects in the scene"
    Step 2 translates the object to world zero. If your mesh is part of a larger scene layout, run Quick Reset on a copy or on a dedicated export collection, not on the original layout objects.

!!! info "What about individual steps?"
    Quick Reset is a shortcut for the most common combination. If you need pivot to centre instead of bottom, or want to apply only modifiers, use the [Individual Steps](individual-steps.md) panel.
