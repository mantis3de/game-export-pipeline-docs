# Validation Rules (Presets)

A **preset** is a named bundle of validation rules — thresholds, which checks run, naming policy, collider conventions, budgets, and per-issue severity overrides — stored in an editable `profiles.json`. Import one and the whole ruleset (including the target engine) loads into the **Rules** tab at once.

The point: a studio defines the rules **once**, hands the artist a single `.json`, and **everyone validates identically** — no transcribing requirements from a PDF.

There are **no built-in presets** — every project's rules are different, so the addon ships with none. You create your own (or receive one from the studio).

---

## Where rules live

- **Presets** tab — load/save the ruleset (`profiles.json`). This is the *input*.
- **Rules** tab — every individual rule (naming, thresholds, budgets, checks, LOD). Importing a preset fills this tab; you can also set it all by hand.
- **Report** (HTML/JSON) — the validation *results* after Run Validation. A different file, the opposite direction. Don't confuse the two.

---

## Importing a preset

In **Asset Preflight → Presets**:

1. Click **Import Preset (.json)** and pick the file (e.g. one a studio sent you, or one exported from the [Specification Editor](specification-editor.md)).
2. Every Rule is set to match the file immediately — engine, thresholds, all 56 granular checks, naming, budgets, severities. There is no dropdown and no separate Apply step.
3. The header shows **Loaded: `<file>.json`** so you always know which spec is driving the rules. If no spec is loaded (or the file was deleted) it shows **Default (no preset)**.
4. **Run Validation** — the model is checked against exactly what the spec asks for.

!!! note "Unchecked in the spec = not validated"
    If the preset unticks a check (say, lightmap UV), the validator will **not** report it. Every master check, every studio rule and all 56 granular findings honour their toggle — validation follows the JSON 1:1, never a hidden default.

---

## The Specification Editor (no Blender needed)

The cleanest way for a studio to author a preset is the standalone **Specification Editor** — a single self-contained HTML file. The client opens it in any browser, ticks the requirements, clicks **Export Preset**, and sends back a `.json` the addon imports directly. See [Specification Editor](specification-editor.md).

---

## Save your own preset (no JSON editing)

Dialled the Rules in by hand and want to reuse them?

1. Click **Save Current Rules as .json…**, choose a location and name (e.g. *Studio — Unity*).
2. The current engine, thresholds, all check toggles, naming policy, collider conventions, budgets, the studio rules and the active severity overrides are written into a `profiles.json`. Re-using a name updates that entry.

Commit the file to your repo and the whole team gets the ruleset. Machine-specific things like export paths are deliberately **not** captured, so the preset stays a portable ruleset.

---

## What a preset controls

| Section | Examples |
|---|---|
| **Engine** | UNITY / UNREAL / GODOT — *export-only*, see below |
| **Thresholds** | max polygons, max texture size, UV padding, texel density, LOD bounds tolerance |
| **Budgets** | per-asset & whole-scene triangles, materials, texture VRAM; **max vertices**, **max object count** |
| **Checks** | the category masters + all 56 granular per-finding toggles |
| **Studio rules** | hidden objects forbidden, packed images forbidden, unique material names |
| **Naming** | strict ASCII, naming template + category/start index, collider prefixes/suffixes |
| **LOD** | LODs required, bounds tolerance |
| **Severity** | per-issue ERROR / WARNING / INFO overrides |

!!! info "Engine is export-only"
    The engine in a preset controls **FBX axes/scale and the LOD file structure at export** — nothing else. It does **not** change validation, naming or collider matching. So switching engine never silently flips a rule. See [Engine Presets](../reference/engine-presets.md).

---

## Editing `profiles.json`

```jsonc
{
  "schema": 1,
  "generator": "SpecificationEditor",        // free-text, for diagnostics
  "version": "1.0.0",
  "profiles": [
    {
      "id": "studio_unity",
      "name": "Studio — Unity",
      "engine": "UNITY",                      // UNITY | UNREAL | GODOT
      "thresholds": { "poly_warn_threshold": 60000, "texture_size_warn": 2048 },
      "budget": { "max_tris": 60000, "max_verts": 40000, "max_object_count": 1,
                  "max_materials": 2, "max_vram_mb": 128 },
      "checks": { "uv_padding_check": true, "naming_default_check": true,
                  "check_packed_images": true, "check_unique_materials": true },
      "naming": { "strict": true, "template": "SM_{Category}_{Name}_{NN}",
                  "category": "Props", "collider_suffixes": ["_col", ".collision"] },
      "lod": { "required": false },
      "severity": { "MISSING_LIGHTMAP": "WARNING", "DEFAULT_NAME": "ERROR" }
    }
  ]
}
```

Any field you omit falls back to a sensible default, so a minimal preset only needs `id`, `name`, and the values you care about. Severity keys are validation **issue IDs** — see [Validation Checks](../reference/validation-checks.md). Full schema: [Profiles Reference](../reference/profiles-reference.md).

---

## How presets affect results

- **Thresholds / checks** change *what* is detected (a 25k-tri mesh is fine at 80k but fails a 20k budget).
- **Severity overrides** change *how serious* a finding is (ERROR / WARNING / INFO) without dropping it. Use them to soften findings that are normal for an asset kind — e.g. an atlas asset where UV overlap should be INFO rather than a warning. (This replaces the old per-kind "Asset Type" presets.) After import, the active overrides are listed read-only in the **Rules** tab (e.g. *Missing lightmap UV → ERROR*) so you can confirm what the spec changed.
- The active spec file and its config are recorded in the exported report (the header reads **Checked against: `<file>`**), so a report always says which ruleset produced it.
