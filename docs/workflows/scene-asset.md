# Workflow — Validate a Scene Asset

The everyday loop: a model is in your scene, make it engine-ready.

> **▶ do · ✓ expect · ⚠ gotcha**

1. **▶ Set the rules.** Asset Preflight → **Presets → Import Preset** (a studio's `.json`), or set thresholds/checks/engine by hand in **Rules**.
2. **▶ Select** the mesh(es). Nothing selected = whole scene.
3. **▶ Validate.** **Scan** tab → **Run Validation**. ✓ The Scan tab fills with a score and grouped issues in place.
4. **▶ Jump to a problem.** Expand an issue → **Select & Focus**. ✓ The exact faces/edges are selected and the **viewport zooms to them** (flipped faces turn red).
5. **▶ Fix.** Repairs are manual, in **Individual Steps** (Safe Repairs, Fix Names, transforms…). Geometry problems are fixed by hand from **Select & Focus**, then **Re-check**.
6. **▶ Re-validate** after any change — results refresh from the last run. Repeat until **EXPORT READY**.
7. **▶ Save / share (optional).** **Report** tab → set a **Report folder** → **Export JSON** (dated snapshot for Compare) or **Export HTML** (shareable report).
8. **▶ Export the model** via the Export panel / your pipeline.

---

## What to check per engine

=== "Unity"

    - Scale applied, 1 unit = 1 m, Y-up.
    - Poly budget: Mobile ≤ 20k, Desktop ≤ 80k.
    - Textures power-of-two, ≤ 2048 (mobile) / 4096 (desktop).
    - Lightmap UV (UV2) **if** you bake lighting (Unity can also auto-generate — kept as info).
    - Materials assigned, no empty slots; clean names.

=== "Unreal"

    - Z-up, X-forward; remember 1 m → 100 cm on import (engine preset handles axes).
    - **Lightmap UV required** for static meshes with baked GI (profile makes it an error).
    - `UCX_`/`UBX_` colliders recognised; collision present.
    - `SM_` prefix on static meshes (auto-set).
    - No non-manifold / degenerate geometry (breaks lightmap bake & collision).

---

## Prove it catches problems (negative tests)

Make a dirty copy and confirm each is flagged:

- Leave scale at 0.01 / 100 → Unapplied Scale.
- Mirror an object → Negative Scale (**error**).
- Delete a face / rip an edge → Non-manifold (**error**).
- Remove the UV map → Missing UV (**error**; Missing Lightmap on Unreal).
- Relink a texture to a missing path → Missing Texture File (**error**).
- 4K texture on a tiny prop → oversized / texel-per-tri warning.
- Name something `Cube` or `Wall 01` (space) → Default Name / Invalid Chars.
