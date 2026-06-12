# Architecture

Game Export Pipeline is a Blender addon built around a clean separation between **settings**, **operators**, and **core logic**. This page explains how the pieces fit together — useful if you want to extend the addon or integrate it into a studio pipeline.

---

## Module overview

```mermaid
graph TD
    subgraph UI["UI (N-Panel)"]
        P1[Game Export Pipeline]
        P2[Engine Scale]
        P3[Quick Reset]
        P4[Individual Steps]
        P5[Collision Setup]
        P6[Scene Organization]
        P7[Validation]
        P8[Fix]
        P9[Export]
    end

    subgraph Operators["Operators"]
        OP_VAL[validation.py]
        OP_EXP[export_fbx.py]
        OP_BAT[batch_export.py]
        OP_COL[collision.py]
        OP_PIV[pivot_tools.py]
        OP_CLN[cleanup.py]
        OP_FIX[fix.py]
        OP_GIZ[gizmo.py]
        OP_PRV[preview.py]
    end

    subgraph Core["Core Logic"]
        C_VAL[validator.py]
        C_EXP[exporter.py]
        C_COL[collision_utils.py]
        C_MEH[mesh_utils.py]
        C_TEX[texture_utils.py]
        C_NAM[naming.py]
    end

    SETTINGS[settings.py<br/>GameExportSettings]

    P7 --> OP_VAL --> C_VAL
    P9 --> OP_EXP --> C_EXP
    P9 --> OP_BAT --> C_EXP
    P5 --> OP_COL --> C_COL
    P3 --> OP_CLN
    P4 --> OP_PIV
    P4 --> OP_CLN
    P8 --> OP_FIX --> C_VAL
    P1 --> OP_GIZ

    SETTINGS --> OP_VAL
    SETTINGS --> OP_EXP
    SETTINGS --> OP_BAT
    SETTINGS --> OP_COL
    SETTINGS --> OP_FIX

    C_EXP --> C_TEX
    C_EXP --> C_NAM
    C_FIX --> C_MEH
```

---

## Settings (`settings.py`)

All user-facing settings are stored in a single `GameExportSettings` `PropertyGroup` attached to `bpy.types.Scene` as `scene.gep_settings`. This means every setting is per-scene and saved in the `.blend` file automatically.

Settings are grouped into logical categories:

| Category | Key properties |
|---|---|
| Output | `export_path`, `use_meshes_subfolder` |
| Engine | `engine_preset`, `custom_axis_forward`, `custom_axis_up`, `custom_scale` |
| Naming | `prefix` |
| Cleanup | `apply_transforms`, `apply_modifiers`, `triangulate`, `remove_empties` |
| Collision | `collision_decimate`, `collision_as_children` |
| Textures | `collect_textures`, `rename_textures`, `use_collection_catalog` |
| Validation thresholds | `poly_warn_threshold`, `texture_size_warn` |
| Viewport helpers | `show_forward_gizmo`, `show_scale_ref` |
| Validation state | `val_ran`, `val_scale_ok`, `val_uvs_ok`, … `val_issues` |

Validation state properties are written by the Validate operator and read by the UI panel to draw the result indicators — the panel itself contains no validation logic.

---

## Operators (`operators/`)

Each operator file registers one or more `bpy.types.Operator` subclasses. Operators are thin: they read settings from `context.scene.gep_settings`, call the appropriate core function, and report results back to Blender via `self.report()`.

| File | Responsibility |
|---|---|
| `validation.py` | Calls `core.validator.validate_objects()`, writes results back to settings |
| `export_fbx.py` | Single-object FBX export with pre-export cleanup on a temporary copy |
| `batch_export.py` | Iterates selected objects and calls export for each |
| `collision.py` | Convex hull and bounding box generation; remove all collision |
| `pivot_tools.py` | Pivot-to-bottom, pivot-to-center operators |
| `cleanup.py` | Apply transforms, apply modifiers, triangulate, lightmap UV |
| `fix.py` | Individual fix operators + Fix All; delegates to `core` utilities |
| `gizmo.py` | Forward axis arrow and scale reference box draw handlers |
| `preview.py` | Export preview utilities |

---

## Core logic (`core/`)

The `core/` package contains pure Python / bmesh logic with no `bpy.ops` calls. This makes it testable outside the Blender UI and easy to call from multiple operators without duplication.

| Module | Responsibility |
|---|---|
| `validator.py` | Runs all eight validation checks and returns a `ValidationResult` with a list of `Issue` dataclass instances |
| `exporter.py` | Builds the FBX export call with the correct axis/scale settings for the active engine preset; handles texture collection |
| `collision_utils.py` | Convex hull and bounding box generation, decimate, naming |
| `mesh_utils.py` | Fill holes, triangulate, remove loose verts and zero-area faces |
| `texture_utils.py` | Collect textures to subfolder, optional rename to `AssetName_TextureType` convention |
| `naming.py` | Prefix application, default name detection, safe-character enforcement |

---

## Data flow: Export

```mermaid
sequenceDiagram
    participant User
    participant ExportOp as export_fbx.py
    participant CoreExp as core/exporter.py
    participant CoreTex as core/texture_utils.py

    User->>ExportOp: Click "Export Active Object"
    ExportOp->>ExportOp: Read gep_settings
    ExportOp->>ExportOp: Duplicate mesh to temp object
    ExportOp->>ExportOp: Apply transforms / modifiers / triangulate (on copy)
    ExportOp->>CoreExp: export_fbx(obj, settings)
    CoreExp->>CoreExp: Build axis + scale kwargs from engine preset
    CoreExp->>CoreExp: Call bpy.ops.export_scene.fbx(...)
    CoreExp->>CoreTex: collect_textures(obj, export_path)
    CoreTex->>CoreTex: Copy texture files to /Textures subfolder
    ExportOp->>ExportOp: Delete temp object
    ExportOp->>User: Report success / errors
```

The original scene object is never modified during export — all cleanup (apply transforms, triangulate, etc.) is performed on a temporary duplicate that is deleted immediately after the FBX is written.

---

## Data flow: Validation

```mermaid
sequenceDiagram
    participant User
    participant ValOp as validation.py
    participant CoreVal as core/validator.py
    participant Settings as gep_settings

    User->>ValOp: Click "Run Validation"
    ValOp->>CoreVal: validate_objects(selected, settings)
    CoreVal->>CoreVal: Run 8 checks per mesh object
    CoreVal->>ValOp: Return ValidationResult(issues=[...])
    ValOp->>Settings: Write val_scale_ok, val_uvs_ok, ... val_issues
    Settings->>User: Panel redraws with green/red indicators
```

Validation is read-only — it never modifies scene data. The `core/validator.py` module uses `bmesh` for mesh inspection and standard Python for all other checks.
