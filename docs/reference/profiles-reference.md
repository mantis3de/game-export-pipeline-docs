# Profiles Reference

The full schema for `profiles.json`. See [Validation Profiles](../features/profiles.md) for how to use and share them.

---

## File shape

```jsonc
{
  "schema": 1,
  "profiles": [ { /* profile */ }, { /* profile */ } ]
}
```

Profiles are resolved in order: the addon's built-in `profiles.json` first, then your **project** file (set via *Presets → Rules file*). A project profile with the same `id` **overrides** the built-in; a new `id` is **added**.

---

## Profile fields

| Field | Type | Default | Notes |
|---|---|---|---|
| `id` | string | — | **required**, unique key |
| `name` | string | — | **required**, shown in the dropdown |
| `engine` | string | `UNITY` | `UNITY` / `UNREAL` / `GODOT` / `CUSTOM` — sets export axes/scale |
| `thresholds.poly_warn_threshold` | int | 100000 | triangle budget |
| `thresholds.texture_size_warn` | int | 4096 | oversized-texture px |
| `thresholds.texel_per_tri_warn` | int | 50000 | texel density warning |
| `thresholds.uv_overlap_max_tris` | int | 200000 | skip overlap check above this |
| `thresholds.uv_padding_resolution` | int | 1024 | lightmap res for padding |
| `thresholds.uv_padding_texels` | int | 4 | min island gap in texels |
| `checks.material_check` | bool | true | run the Materials category |
| `checks.texture_check` | bool | true | run texture checks |
| `checks.uv_overlap_check` | bool | true | run UV-overlap check |
| `checks.uv_padding_check` | bool | true | run lightmap-padding check |
| `checks.naming_default_check` | bool | true | flag default Blender names |
| `checks.texel_density_check` | bool | false | flag assets outside the texel-density band (opt-in) |
| `thresholds.texel_density_target` | int | 1024 | target texel density (px/m) |
| `thresholds.texel_density_tolerance` | float | 2.0 | multiplicative pass band around the target |
| `naming.strict` | bool | false | ASCII-only names |
| `naming.template` | string | "" | studio naming template, e.g. `SM_{Category}_{Name}_{NN}` |
| `naming.collider_prefixes` | string[] | *(built-in)* | override collider prefixes |
| `naming.collider_suffixes` | string[] | *(built-in)* | override collider suffixes |
| `lod.required` | bool | false | profile expects LOD chains (informational) |
| `budget.max_tris` | int | 100000 | per-asset triangle budget |
| `budget.max_materials` | int | 4 | per-asset material / draw-call budget |
| `budget.max_vram_mb` | int | 256 | per-asset texture-memory budget (MB) |
| `budget.scene_max_tris` | int | 2000000 | whole-scene triangle budget (dashboard) |
| `budget.scene_max_materials` | int | 150 | whole-scene material budget |
| `budget.scene_max_vram_mb` | int | 4096 | whole-scene texture-memory budget (MB) |
| `release.min_health` | int | 85 | minimum average health to pass the [release gate](../features/release-gate.md) |
| `severity` | object | `{}` | per-issue overrides: `{ ISSUE_ID: "ERROR"\|"WARNING"\|"INFO" }` |

Any omitted field falls back to its default, so a minimal profile is just `id`, `name`, and the few values you care about.

---

## Minimal example

```jsonc
{
  "id": "studio_unreal",
  "name": "Studio — Unreal",
  "engine": "UNREAL",
  "thresholds": { "poly_warn_threshold": 90000 },
  "severity": { "MISSING_LIGHTMAP": "ERROR", "MISSING_COLLISION": "ERROR" }
}
```

---

## Notes

- **Severity layering.** Overrides are applied *last*, after the engine preset and Marketplace Strict — so the profile always wins.
- **`severity` keys are issue IDs.** See [Validation Checks](validation-checks.md) for the catalogue.
- **Recorded in reports.** The active profile id and its severity overrides are written into the report's `config`, so a report always states which ruleset produced it.
- **Validation.** Bad profiles are reported on load (missing `id`/`name`, invalid severity value, unknown `engine`) and skipped rather than crashing.
