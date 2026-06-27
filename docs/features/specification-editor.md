# Specification Editor

The **Specification Editor** is a single, self-contained HTML file that lets a studio (or client) author a validation preset **without Blender, without the addon, and without internet**. They tick the project requirements in a browser, click **Export Preset**, and send back a `.json` the addon imports directly.

This turns a fuzzy "requirements PDF" into a machine-readable spec that the artist's validator understands 1:1.

---

## The workflow

1. **You** send the studio `SpecificationEditor.zip` (the HTML + a short README). It works offline — they just double-click the HTML.
2. **The studio** fills in the project requirements: target engine, triangle/vertex/material limits, naming convention, UV/LOD/collision rules, and any of the individual checks.
3. They click **Export Preset (.json)** at the bottom. The browser downloads `‹project›.profiles.json`.
4. **You** receive that file and, in **Asset Validator → Presets → Import Preset (.json)**, load it. Every Rule is now set to the studio's spec.
5. **Run Validation** — the model is checked against exactly what the studio asked for.

The same `.json` is the single source of truth: it drives validation and can be re-imported by anyone on the team.

---

## What the editor covers

The editor mirrors the addon's **Rules** tab exactly — nothing the validator can check is missing:

- **Engine** (Unity / Unreal / Godot) and project name.
- **Limits & budgets** — triangles, **vertices**, **object count**, materials, texture VRAM (per-asset and whole-scene), max texture size, UV padding, texel density, LOD bounds tolerance.
- **Naming** — strict ASCII, naming template, category and start index, collider prefixes/suffixes.
- **LOD required.**
- **Severity overrides** — set any common finding to ERROR / WARNING / INFO (e.g. atlas asset → UV overlap = INFO, baked-lighting asset → missing lightmap = ERROR). This replaces the old per-kind "Asset Type" presets.
- **Checks** — every category master + all 56 granular per-finding toggles (grouped by category under an *Advanced* expander).

Each exported file is stamped with `generator` and `version` so you can always tell where a preset came from when diagnosing.

---

## Editing an existing spec

The editor is two-way: click **Import Preset** to load an existing `.profiles.json` and the whole form fills in (engine, limits, naming, checks, severities). Change what you need and **Export Preset** again. This makes specs reusable living documents — a lead keeps a base spec, imports it per project, tweaks a few values, and re-exports, instead of re-filling the form each time. It also lets anyone open a received `.json` in a readable form to review it.

## Why HTML and not a server

A single local HTML file:

- needs no install, no account, no internet — the client just opens it;
- can be zipped and emailed;
- is the exact same file you could later host at a URL with no changes.

The editor knows nothing about Blender — only the JSON schema. So the same file keeps working across addon versions as long as the schema is compatible (`schema: 1`).

---

## Keeping it in sync

The editor is generated from the addon's own rule schema, so it stays complete. If a future addon version adds a new rule, the editor is regenerated to include it, and the `schema` number guards compatibility. Always send the studio the editor that matches your addon version.
