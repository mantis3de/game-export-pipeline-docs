# Validation

The **Validation** panel runs eight checks against every selected mesh object and reports the results as green (pass) or red (fail) indicators directly in the panel.

---

## Running validation

Select one or more mesh objects, then click **Run Validation**. The scan is fast — it uses pure Python and bmesh with no operator calls. Results appear immediately below the button.

!!! tip "Run validation before every export"
    It takes two seconds and catches the issues that cause broken imports. Make it part of your export habit.

---

## Thresholds

Before running, you can adjust two thresholds that control when a warning is raised:

**Max Polygons** — objects with more faces than this value get flagged as high-poly. Default is `100,000`. Raise this for hero assets, lower it for background props.

**Max Tex Size (px)** — textures larger than this dimension (width or height) get flagged. Default is `4096`. Adjust based on your project's texture budget.

---

## Result indicators

After a run, eight indicators appear — one per check category. A green checkmark means no issues were found in that category. A red error icon means at least one issue was found.

| Indicator | What is checked |
|---|---|
| **Scale** | Unapplied scale or rotation, negative scale, non-uniform scale, inherited parent scale |
| **UV** | Missing UV map, UV coords outside the 0–1 range |
| **Lightmap UV** | Presence of a second UV channel (required for baked lighting) |
| **Mesh** | N-gons, non-manifold edges (holes), zero-area faces, loose vertices, poly count |
| **Materials** | Missing material slots, empty material slots |
| **Textures** | Missing texture files on disk, non-power-of-two texture sizes, oversized textures |
| **Naming** | Default Blender names (Cube, Sphere, etc.), special characters in object name |
| **Collision** | Missing `UCX_` or `UBX_` collision object |

---

## Issues list

If any issues were found, a scrollable **Issues** section appears below the indicators with a plain-text description of each problem and the object it belongs to — for example:

```
Rock_01: Unapplied scale (1.500, 1.500, 1.500)
Barrel_02: No UV maps — textures won't work in engine
Barrel_02: Missing lightmap UV (2nd UV channel)
```

The list is capped at 25 lines in the panel. For long lists, use the [Fix](fix.md) panel's **Fix All** button to resolve everything at once.

---

## After fixing

After running the Fix operators, click **Run Validation** again to confirm all indicators are green before exporting. Validation does not run automatically after a fix — you trigger it manually.

→ Full description of every check: [Validation Checks](../reference/validation-checks.md)
