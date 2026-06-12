# API Reference

Game Export Pipeline is designed for use through its N-Panel UI, but every operator and core function is also callable from Python — useful for studio pipeline scripts, custom export buttons, or integration with other addons.

!!! info "Access pattern"
    Settings live on `bpy.context.scene.gep_settings`. Operators are called via `bpy.ops.gep.*`. Core functions in `core/` can be imported and called directly.

---

## Settings — `scene.gep_settings`

All settings are properties on the `GameExportSettings` PropertyGroup attached to every scene.

```python
s = bpy.context.scene.gep_settings

# Read
print(s.export_path)      # e.g. "//GameAssets"
print(s.engine_preset)    # 'UNITY' | 'UNREAL' | 'GODOT' | 'CUSTOM'
print(s.prefix)           # e.g. "SM_"

# Write
s.export_path    = "//Export"
s.engine_preset  = 'UNREAL'
s.prefix         = "SM_"
s.triangulate    = True
s.collect_textures = True
```

### Output

| Property | Type | Default | Description |
|---|---|---|---|
| `export_path` | `str` | `"//GameAssets"` | Root folder for exported FBX files |
| `use_meshes_subfolder` | `bool` | `True` | Place exports inside a `/Meshes` subfolder |

### Engine

| Property | Type | Default | Description |
|---|---|---|---|
| `engine_preset` | `enum` | `'UNITY'` | `'UNITY'`, `'UNREAL'`, `'GODOT'`, `'CUSTOM'` |
| `custom_axis_forward` | `enum` | `'-Z'` | Forward axis for Custom preset |
| `custom_axis_up` | `enum` | `'Y'` | Up axis for Custom preset |
| `custom_scale` | `float` | `1.0` | Scale factor for Custom preset |

### Cleanup (applied during export on a copy)

| Property | Type | Default | Description |
|---|---|---|---|
| `apply_transforms` | `bool` | `True` | Bake transforms before export |
| `apply_modifiers` | `bool` | `True` | Evaluate modifier stack before export |
| `triangulate` | `bool` | `True` | Convert to tris before export |
| `collect_textures` | `bool` | `True` | Copy textures to `/Textures` subfolder |

### Collision

| Property | Type | Default | Description |
|---|---|---|---|
| `collision_decimate` | `float` | `0.3` | Decimate ratio for generated collision (0 = off) |
| `collision_as_children` | `bool` | `False` | Parent colliders to render mesh (Unity mode) |

### Validation thresholds

| Property | Type | Default | Description |
|---|---|---|---|
| `poly_warn_threshold` | `int` | `100000` | Face count above which a warning is raised |
| `texture_size_warn` | `int` | `4096` | Texture dimension above which a warning is raised |

### Validation state (read-only after a validation run)

| Property | Type | Description |
|---|---|---|
| `val_ran` | `bool` | `True` after the first validation run |
| `val_scale_ok` | `bool` | Scale check passed |
| `val_uvs_ok` | `bool` | UV check passed |
| `val_lightmap_ok` | `bool` | Lightmap UV check passed |
| `val_mesh_ok` | `bool` | Mesh topology check passed |
| `val_materials_ok` | `bool` | Material check passed |
| `val_textures_ok` | `bool` | Texture check passed |
| `val_naming_ok` | `bool` | Naming check passed |
| `val_collision_ok` | `bool` | Collision check passed |
| `val_issues` | `str` | Newline-separated list of all issue messages |

---

## Operators — `bpy.ops.gep`

All operators read settings from `context.scene.gep_settings` unless noted.

```python
# Select objects first, then call operators
bpy.ops.object.select_all(action='SELECT')

# Validate selection
bpy.ops.gep.validate_objects()

# Export active object
bpy.ops.gep.export_fbx()

# Batch export selected
bpy.ops.gep.batch_export_fbx()

# Collision
bpy.ops.gep.gen_convex_hull()
bpy.ops.gep.gen_box_collision()
bpy.ops.gep.remove_collision_meshes()

# Pivot
bpy.ops.gep.pivot_bottom()
bpy.ops.gep.pivot_center()

# Cleanup
bpy.ops.gep.apply_transforms()
bpy.ops.gep.apply_modifiers()
bpy.ops.gep.triangulate()
bpy.ops.gep.add_lightmap_uv()
bpy.ops.gep.prepare_for_export()   # pivot + origin + apply scale (Quick Reset)

# Fix
bpy.ops.gep.fix_scale()
bpy.ops.gep.fix_negative_scale()
bpy.ops.gep.fix_holes()
bpy.ops.gep.fix_ngons()
bpy.ops.gep.fix_loose_verts()
bpy.ops.gep.fix_zero_faces()
bpy.ops.gep.fix_missing_material()
bpy.ops.gep.fix_empty_slots()
bpy.ops.gep.fix_naming()
bpy.ops.gep.fix_missing_uv()
bpy.ops.gep.fix_missing_collision()
bpy.ops.gep.fix_all()

# Scene organization
bpy.ops.gep.organize_collections()
bpy.ops.gep.dissolve_collections()
```

---

## Core — `core/validator.py`

Call `validate_objects` directly when you need validation results in a script without writing to scene settings.

```python
from game_export_pipeline.core.validator import validate_objects

objects = [o for o in bpy.context.selected_objects if o.type == 'MESH']
settings = bpy.context.scene.gep_settings

result = validate_objects(objects, settings)

print(f"Errors:   {len(result.errors)}")
print(f"Warnings: {len(result.warnings)}")

for issue in result.issues:
    print(f"[{issue.severity}] {issue.obj_name} — {issue.message}")
```

### `ValidationResult`

| Member | Type | Description |
|---|---|---|
| `issues` | `list[Issue]` | All issues found |
| `has_errors` | `bool` | `True` if any issue has `severity == 'ERROR'` |
| `errors` | `list[Issue]` | Issues with `severity == 'ERROR'` |
| `warnings` | `list[Issue]` | Issues with `severity == 'WARNING'` |
| `category_ok(type)` | `bool` | Returns `True` if no issue of that `issue_type` was found |

### `Issue`

| Field | Type | Values |
|---|---|---|
| `obj_name` | `str` | Name of the object the issue belongs to |
| `issue_type` | `str` | `'SCALE'`, `'UV'`, `'LIGHTMAP'`, `'MESH'`, `'MATERIAL'`, `'TEXTURE'`, `'NAMING'`, `'COLLISION'` |
| `message` | `str` | Human-readable description |
| `severity` | `str` | `'ERROR'` or `'WARNING'` |

---

## Example: pipeline script

```python
import bpy
from game_export_pipeline.core.validator import validate_objects

def export_all_meshes():
    s = bpy.context.scene.gep_settings
    s.export_path    = "//Export"
    s.engine_preset  = "UNREAL"
    s.prefix         = "SM_"
    s.triangulate    = True
    s.collect_textures = True

    meshes = [o for o in bpy.data.objects if o.type == 'MESH'
              and not o.name.startswith(('UCX_', 'UBX_'))]

    # Validate first
    result = validate_objects(meshes, s)
    if result.has_errors:
        print("Errors found — running Fix All before export")
        bpy.ops.object.select_all(action='DESELECT')
        for obj in meshes:
            obj.select_set(True)
        bpy.ops.gep.fix_all()

    # Export
    for obj in meshes:
        bpy.ops.object.select_all(action='DESELECT')
        obj.select_set(True)
        bpy.context.view_layer.objects.active = obj
        bpy.ops.gep.export_fbx()
        print(f"Exported: {obj.name}")

export_all_meshes()
```
