# Report Schema

The addon produces two report shapes, both JSON and both designed to be diffed and consumed by tooling. They are **deterministic**: the same meshes + the same ruleset always produce the same content hash (only the timestamp varies).

---

## Scene report (single-scene validation)

Written by **Export Report** in the window. Schema **1.1**.

Top-level keys: `content_hash`, `schema_version`, `scope`, `config`, `summary`, `assets`, `generated_at`, `generator`.

- **`content_hash`** — `sha256:` over the canonical core (excludes `generated_at`). Same input ⇒ same hash.
- **`config`** — the ruleset that produced the report: engine, thresholds, checks, `active_profile`, `profile_severity_json`, collider lists, etc.
- **`summary`** — `score_overall` (export gate), `scene_quality_score`, `export_status`, `errors` / `warnings` / `infos`, and `scores { geometry, transform, normals, uv, materials, textures, naming, collision, lod }`.
- **`assets[]`** — per asset: `name`, `mesh`, `score`, `status`, counts, and `issues[] { id, type, severity, component, count, fix, message }`.

```jsonc
"summary": {
  "score_overall": 47,
  "scene_quality_score": 90,
  "export_status": "NOT EXPORT READY",
  "errors": 11, "warnings": 26, "infos": 24,
  "scores": { "geometry": 0, "uv": 67, "lod": 100, "...": 100 }
}
```

!!! note "Two scores"
    `score_overall` is the pooled **export gate**; `scene_quality_score` is the **mean per-asset** quality. See [Core Concepts](../getting-started/concepts.md).

---

## Batch report (folder scan)

Written by **Scan Folder** and the CLI, as `asset_preflight_report.json` (+ `.html`). Schema `batch-1.0`.

Top-level keys: `schema`, `generated_at`, `profile`, `summary`, `project`, `release`, `regressions`, `rules`, `duplicates`, `files`, `history`.

- **`summary`** — `total`, `passed`, `warnings`, `failed`, `import_failed`, `ignored`, `avg_health`, `risk`.
- **`project`** — `totals` (summed `meshes`/`tris`/`materials`/`vram_mb`) and the `budget` it is measured against (the project limits).
- **`release`** — the release-gate verdict: `status` (`READY`/`BLOCKED`), `reasons[]` (why it's blocked), and `min_health` (the threshold used).
- **`regressions`** — comparison to the previous report (omitted/empty when there is none): `health` (`prev`/`now`/`delta`), `totals` (per metric `prev`/`now`/`pct`), and `worst[]` (assets with the biggest score drops). See [Release Gate & Regressions](../features/release-gate.md).
- **`rules[]`** — actionable findings grouped by rule for the **By rule** view: `id`, `severity`, `count`, `assets[]`. INFO findings are excluded.
- **`duplicates`** — groups of files sharing a mesh signature (accidental re-exports).
- **`files[]`** — per file: `file`, `path`, `import_ok`, `status` (`PASS`/`WARNING`/`FAIL`/`IMPORT_FAILED`), `score`, `errors`/`warnings`/`infos`, `ignored`, `meshes`, `tris`, `materials`, `vram_mb`, `signature`, `fingerprint` (mtime+size for delta), `cached`, `thumb` (base64 PNG when thumbnails are on), and a trimmed `issues[]`.
- **`history`** — recent runs' `avg_health` for the trend sparkline.

```jsonc
"summary": {
  "total": 6, "passed": 3, "warnings": 1, "failed": 1, "import_failed": 1,
  "avg_health": 83, "risk": "Export Not Recommended"
}
```

---

## Why determinism matters

- **CI** can compare the hash / JSON across commits to see exactly what changed.
- **Delta scans** reuse a file's previous entry when its fingerprint is unchanged.
- **Trust** — a report can't silently vary between machines given the same inputs.

The validation core is a stable contract (see `FROZEN_API.md` in the addon): changes are additive (new issue IDs, new optional fields); anything breaking bumps the schema version.
