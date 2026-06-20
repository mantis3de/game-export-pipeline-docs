# Core Concepts

A short mental model of how Game Export Pipeline thinks. Five ideas explain almost everything else.

---

## 1. Validation is a ruleset, not a verdict

The addon never *blocks* you. It runs a set of checks and reports findings at three severities:

- **Error** — will break or badly degrade the asset in-engine (missing UV, non-manifold, missing texture).
- **Warning** — worth attention, often situational (UV overlap, missing lightmap on a non-baked asset).
- **Info** — neutral facts (face breakdown, custom normals present).

Which check is an error vs a warning depends on the active **profile** (see below). You can always export; the report just tells you the risk.

---

## 2. Two scores, deliberately different

After a run you see two numbers — they answer different questions:

- **Export Gate (`score_overall`)** — a single *pooled* score over all issues. One blocking error anywhere drives the relevant category toward zero. This is the "is anything blocking?" gate that drives `EXPORT READY` / `NOT EXPORT READY`.
- **Scene Quality** — the *mean per-asset* score. Clean assets count as 100, so this reflects average asset health.

A scene with one badly broken mesh and nine perfect ones has a **low gate** (something's blocking) but a **high scene quality** (most assets are fine). Both are correct; they measure different things.

---

## 3. Profiles carry the rules

A **profile** is a named bundle of thresholds, check toggles, naming policy, collider conventions and per-issue severity overrides — stored in an editable `profiles.json`. Pick a profile and **Apply** it to load all of that at once.

The big win for teams: commit one `profiles.json` to your repo and everyone — and CI — validates identically. See [Validation Profiles](../features/profiles.md).

---

## 4. In-scene vs batch

The same validator runs in two modes:

| | In-scene | Batch |
|---|---|---|
| **What** | Meshes in your open scene | A folder of exported files |
| **Where** | Asset Preflight → Scan → Run Validation | Asset Preflight → Scan → Scan Folder, or the CLI |
| **Output** | The Results tab + optional JSON/CSV | A shareable HTML report + JSON |
| **Who** | The artist, while working | The lead / CI, before publish |

Batch imports each file into a throwaway scene, validates it in isolation, then removes it — your open scene is never touched.

---

## 5. Determinism

The exported report is **content-hashed** and reproducible: the same meshes + the same ruleset always produce the same report (only the timestamp varies). That makes reports diffable across runs and trustworthy in CI. See [Report Schema](../reference/report-schema.md).

---

## Where things live

- **Asset Preflight window** — validate, scan, fix, read the report (the daily driver). → [The Asset Preflight Window](../window.md)
- **Viewport tools** (N-panel) — things that must touch the viewport live: Collision, LOD generation/preview, Quick Reset, Export, FBX tools.
- **`profiles.json`** — the rules (input). **Export Report / HTML** — the results (output). Two different files, two directions.
