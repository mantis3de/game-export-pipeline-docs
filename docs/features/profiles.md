# Validation Profiles

A **profile** is a named bundle of validation rules — thresholds, which checks run, naming policy, collider conventions, and per-issue severity overrides — stored in an editable `profiles.json`. Pick one, click **Apply**, and the whole ruleset loads at once.

The point: commit one `profiles.json` to your project repo and **everyone — and CI — validates identically**.

---

## Using a profile

In the **Asset Preflight → Presets** tab:

1. Pick a profile from the **Profile** dropdown.
2. Click **Apply Profile** — its thresholds, checks, severities, engine and naming policy are written into the current settings.
3. **Run Validation** to use them.

Click **Reload** after editing `profiles.json` on disk, so the new values appear.

!!! note "Apply is explicit"
    Selecting a profile in the dropdown doesn't change anything until you click **Apply** — so a profile never silently overwrites manual tweaks.

---

## Built-in profiles

| Profile | Max tris | Max tex | Naming | LOD | Notable severities |
|---|---:|---:|---|---|---|
| **Unity — Mobile** | 20,000 | 2048 | strict | required | HIGH_POLY, oversized & non-PoT textures → **error**; lightmap → info |
| **Unity — Desktop** | 80,000 | 4096 | relaxed | — | lightmap → info |
| **Unreal — Desktop** | 120,000 | 4096 | relaxed | — | missing lightmap → **error** |
| **Godot 4** | 100,000 | 4096 | relaxed | — | defaults |
| **Generic — Marketplace** | 50,000 | 4096 | strict | — | missing lightmap, non-PoT, missing collision, default/invalid names → **error** |
| **Mobile — Low-End** | 10,000 | 1024 | strict | required | high-poly, oversized & non-PoT textures → **error** |
| **Mobile — High-End** | 35,000 | 2048 | strict | required | high-poly, oversized textures → **error** |
| **PC — Indie** | 150,000 | 4096 | relaxed | — | lightmap → info |
| **VR (90 fps)** | 30,000 | 2048 | strict | required | high-poly, oversized textures → **error** |

Each profile also carries per-asset and whole-scene **budgets** (triangles, materials,
texture VRAM) — see [Budgets & Dashboard](budgets.md). These are starting points —
copy and tune them for your studio.

---

## Sharing rules with the team

1. Copy the addon's `profiles.json` into your project repo, e.g. `Project/preflight/profiles.json`.
2. Edit it in any text editor (no Python).
3. In **Presets → Rules file**, point at that path. Your project profiles **override** built-ins with the same `id` and **add** new ones.
4. Commit it. Teammates pull and point the addon at the same file → identical rules.

!!! tip "Rules in vs report out"
    **Rules file** is the **input** (the rules you load). **Export Report** / the HTML batch report is the **output** (the results). Two different files, two directions — don't confuse them.

---

## Editing `profiles.json`

```jsonc
{
  "schema": 1,
  "profiles": [
    {
      "id": "studio_unity",
      "name": "Studio — Unity",
      "engine": "UNITY",                     // UNITY | UNREAL | GODOT | CUSTOM
      "thresholds": {
        "poly_warn_threshold": 60000,
        "texture_size_warn": 2048
      },
      "checks": { "uv_padding_check": true, "naming_default_check": true },
      "naming": {
        "strict": true,
        "collider_suffixes": ["_col", ".collision"]   // optional override
      },
      "lod": { "required": false },
      "severity": {
        "MISSING_LIGHTMAP": "WARNING",       // ERROR | WARNING | INFO
        "DEFAULT_NAME": "ERROR"
      }
    }
  ]
}
```

Any field you omit falls back to a sensible default, so a minimal profile only needs `id`, `name`, and the few values you care about. Severity keys are validation **issue IDs** — see [Validation Checks](../reference/validation-checks.md). Full schema: [Profiles Reference](../reference/profiles-reference.md).

---

## How profiles affect results

- **Thresholds / checks** change *what* is detected (e.g. a 25k-tri mesh is fine on Desktop but fails Mobile's 20k budget).
- **Severity overrides** change *how serious* a finding is — layered last, after the engine preset and Marketplace Strict, so the profile wins.
- The active profile and its severity overrides are recorded in the exported report's `config`, so a report always says which ruleset produced it.
