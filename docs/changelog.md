# Changelog

All notable changes are documented here. The format follows [Keep a Changelog](https://keepachangelog.com/), versioning follows [Semantic Versioning](https://semver.org/).

---

## [1.8.x] — Isolate, LOD browsing & data-safety hardening

Workflow polish around validation browsing and LOD review, plus a release audit
that hardened the preview/isolate features against data loss.

### Validation list & Isolate
- **Explicit Isolate mode.** Clicking a row in the Scope/Scan list selects and highlights the object but no longer hides the rest by default. Press **Isolate Selected** to enter Isolate mode — then clicking rows shows only that object *and* scopes the report to it; **Exit Isolate (show all)** leaves the mode. This keeps **By Category** browsing usable (the report shows every object outside Isolate).
- **Isolate restores your visibility exactly.** Entering Isolate snapshots each object's visibility and exiting restores it 1:1 — objects you had hidden stay hidden (no more "show everything" on exit).
- **Scope list shows no error counts.** The Scope picker is a selection list only; error/warning counts live in the Scan results list where they belong.
- **Scene-wide findings are always shown and ignorable.** Findings with no single object (e.g. duplicate materials) appear under a **Scene-wide** group and can be muted with the ghost/globe icons (stored per scene).
- **Natural object order.** The object list sorts like the outliner (`Brazier_01, _02, … _10`) and keeps your highlighted row after a fix instead of jumping.

### LOD tools
- **Detail slider.** One master **Detail** control above keep % makes every LOD denser as you slide it up (0 % = use keep %, 100 % = barely decimated). Detail affects geometry only — never the switch %.
- **Generate regenerates.** Pressing **Generate LODs** again rebuilds exactly the configured number of levels instead of stacking new ones (no more LOD4…LOD9).
- **LOD browser list.** A new **Objects with LODs** list in the Preview box drives everything: click a row to isolate and preview that chain (chain-aware — all levels stay eligible, distance picks which shows). The standalone *Preview LODs* toggle was replaced by this list plus **Exit Preview**. Colliders are always hidden during preview.
- **Tri counts in preview.** The preview readout shows the live triangle count of the visible LOD (both the distance slider and Live).
- **Preview never mutates data.** Previewing computes switch %/cull/FOV in memory; it no longer writes the stored policy. Persisting a policy stays with **Apply / Refresh Policy**.

### Scene Cleanup, materials, reports
- **Collider un-parenting.** Scene Cleanup now detaches colliders from their mesh parent (studio rule: colliders are siblings; parenting happens only at export), recognising a wide set of suffixes (`_col`, `_collision`, `_ucx`, `_phys`, `_hitbox`, …) and keeping world transform.
- **Unique Material Names on by default.** Duplicate import materials (`Material.001`, `.002`…) are flagged out of the box; the Select & Focus arrow opens the material editor on one of the duplicates so you decide what to keep.
- **Per-object LOD breakdown in the HTML report.** Each object card lists its LOD chain with triangle counts, computed from the real scene meshes (works for hand-made LODs too).

### Panels & fixes
- **Viewport Helpers** is now its own panel at the top of the GameExport tab (Forward Axis + Scale Ref); the helper objects are non-selectable so Pivot/Origin can't move them.
- **Pivot + Origin + Apply Scale** keeps your original selection after running.
- **Missing-texture check** resolves paths like Blender (`filepath_from_user`) and respects packed / in-memory images, so a relinked texture stops being flagged.
- Edit-Mode crash guards on all UV checks; lightmap-padding distance measured on real UV geometry (no false positives).

---

## [Unreleased] — Asset Preflight

A major workflow update turning the panel sprawl into one tabbed window and adding
team / pipeline tooling.

### Spec-driven rules, engine-as-export, panel cleanup
- **Specification Editor** — a new standalone HTML file a studio fills in (no Blender, no install, offline) to author a preset; **Export Preset** downloads a `profiles.json` the addon imports directly. Mirrors every Rule, including all 56 granular checks. See [Specification Editor](features/specification-editor.md).
- **Import Preset (.json)** — Presets tab now imports a spec file and sets **every** Rule to match it in one step; the header shows **Loaded: `<file>`** (or *Default (no preset)* when none/missing). The profile dropdown and the built-in presets were removed — every project's rules differ, so none ship by default.
- **Rules tab is the single ruleset** — Asset Type, Naming (template + category/start index) and Collider names moved here from Presets; tab order is now **Presets · Rules · Scope · Scan · Report**. The Rules layout mirrors the Specification Editor (limits together, checks together).
- **Engine is export-only** — the engine setting now controls **only** FBX axes/scale and the LOD/collider file structure at export. It no longer changes validation, naming or matching. The **Custom** engine and **Marketplace Strict** (an opaque severity bundle) were removed; studios set the individual severities they need in the preset.
- **Engine-aware export** — **Export Selected** is asset-group aware: Unity/Godot get one nested FBX (`_LOD#` + child collider); Unreal gets the base + **separate `_LOD#.fbx`** files. New **Subfolder per asset** (all engines) and **Textures in /Textures subfolder** toggles. Collider↔mesh matching is `SM_`/index-agnostic, so `UBX_SM_Chair_01` pairs with `SM_Chair_01`.
- **New studio rules (opt-in)** — Max Vertices, Max Object Count, Hidden Objects Forbidden, Packed Images Forbidden, Unique Material Names. LODs are excluded from the Scope/Scan list (validated as chains, not per-mesh).
- **Asset Type removed** — the per-kind severity presets (PBR/Lightmap/Procedural/Kitbash) were dropped; a studio now expresses the same adjustments directly via the preset's `severity` map (downgrade UV overlap to INFO, raise missing lightmap to ERROR, …), which the Specification Editor now exposes. One path, no hidden layer.
- **Fix Names** — one manual button renames a whole asset group to engine-correct names (`SM_` / `_LOD#` / `UCX_`/`UBX_`), pulling LODs and colliders in by name. Automatic SM_ renaming on safe-fix was removed.
- **Panel consolidation** — the standalone *Quick Reset* and *Fix* panels were folded into **Individual Steps** (Prepare · Naming · Pivot/Origin · Transforms & Mesh · UV · Safe Repairs · Cleanup); the *Engine Scale* panel was removed; *Add Missing Material* and the bulk *Fix Safe Issues* were removed (repairs are manual now). Viewport Helpers split into per-engine **Forward Axis: Unity/Unreal** and a single **Scale Ref**.
- **LOD simplification** — you set only **keep %**; the **switch %** is derived automatically (manual under *Advanced*). LODs are always triangulated, the base is always validated first, and *Export LOD Chains* / *Preserve Symmetry* were removed. The LOD-validation toggle lives in Rules and validates any chain (generated, imported or hand-made).
- **Report** — the HTML header now reads **Checked against: `<spec>`**; files with errors/warnings are **expanded by default** so problems are visible without clicking; the config snapshot records the new rules for accurate delta/regression.
- **Severity overrides visible** — after importing a preset, its per-issue severity overrides are listed read-only in the Rules tab (e.g. *Missing lightmap UV → ERROR*), so the artist can confirm exactly what the spec changed.

### Bug reporting & release polish (1.4.x – 1.5.x)
- **Built-in bug reporter** — add-on errors are captured (traceback + scrubbed context) instead of crashing Blender, and surfaced under **Report a problem** in the main panel. Send via **GitHub Issue** (pre-filled), **Copy**, **Save .txt** or **Email**; a **Crash Log** button opens Blender's own `*.crash.txt` for hard crashes. **Offline, user-initiated, no telemetry** — local paths are scrubbed. See [Bug Reporting & Diagnostics](features/bug-reporting.md).
- **Pre-release cleanup** — removed the dead Import module and excluded dev files (tests, CI, internal specs) from the released add-on; manifest is store-ready (GPL-3.0, permissions, Blender 4.2+).

### Granular checks & UX (1.2.x – 1.3.x)
- **56 per-finding toggles** — every category (Geometry, Normals, Transforms, UV, Materials, Textures, Naming, Collision, LOD, Budgets) breaks into individual findings via a **▸ arrow** next to its master toggle, so you can build a very precise ruleset. A single central filter drives them; defaults are all-on.
- **Profiles carry the checks** — selecting a profile sets the category masters *and* the granular toggles. Built-in presets now differ in *what they check* (e.g. **Mobile — Low-End** unticks Normal/Metallic map checks; **Marketplace** stays strict). **Save Current as Profile…** round-trips the whole set.
- **No Apply button** — picking a profile applies it immediately (engine, naming, checks, thresholds).
- **Progress bars for big scenes** — above ~200 objects, Run Validation, Fix Safe and Export run in batches with a **viewport-header progress bar** (`Validating 120/400 — Esc to cancel`); the UI no longer freezes.
- **Export reuses the last result** — Export HTML/JSON renders the report from the last Run/Scan instead of re-validating; on big scenes it only renders thumbnails.
- **List ↔ viewport sync** — clicking a list row selects the object in the scene, and selecting an object in the viewport / Outliner highlights its row. The Scope list gained an **All** tick-all toggle.
- **"No <map> texture connected"** moved from the Materials category to **Textures** (it's about missing maps), gated by Check Textures.
- **One Export box** — folder, engine, prefix, FBX/glTF, the Export button and auto-cleanup are all in the single **Export** section at the top; the separate Export panel and the FBX Tools tab were removed.
- **Floating-window niceties** — By-Category view has **Expand / Collapse all**; each section header has a **▲ back-to-top** (popups can't scroll); long issue lists are capped in the popup with a pointer to the docked sidebar.
- **Textured thumbnails** — report previews now render with Workbench *Texture* shading.
- **Report folder required** — Scene reports are written only to the folder you set (no temp fallback).

### Report workflow overhaul (1.1.x)
- **Scan / Report split** — the window tabs are now **Presets · Scope · Rules · Scan · Report**. **Run Validation only validates** (and shows results on screen); saving and exporting moved to the **Report** tab.
- **Export = save** — **Export JSON** writes a dated `history/` snapshot (JSON only) and adds it to the history list; **Export HTML** builds/opens the shareable report and does *not* add a history entry.
- **Report folder required** — Scene reports are written **only** to the Report folder you set (no temp / next-to-blend fallback); a red warning and a blocked export appear until one is set. **Open Report Folder** reveals it.
- **Compare Selected** — tick **2+** snapshots → one **Quality Diff** (oldest vs newest) with a `%` improvement, and separate **Fixed / New / Ignored** lists. The line-chart "trend" view was dropped in favour of this single layout.
- **Ignored ≠ fixed** — ignored findings now count as **present** in open-issue totals and comparisons, so muting an issue can never read as a fix. The Diff shows an **Ignored (muted)** category, flagging items muted this period.
- **History row preview** — each snapshot row has a 🔗 icon that renders its HTML on demand and opens it.
- **Thumbnails** — rendered **only for the HTML export** (never on Run), now using Workbench **Texture** shading so previews match the viewport.
- **Cleanup** — the **FBX Tools** tab (and its Import button) was removed (use Blender's native Import); the Better-FBX status moved into the Export panel. **Engine** is set in one place (top of the main panel), no longer duplicated in Presets.
- **List → viewport selection** — clicking a row in the object list (the highlight, not the tick) selects that object in the scene and makes it active, so the viewport follows the list.
- **No per-issue fixer** — the per-finding *Fix This* button (and its operator) was removed; all safe repairs run from the single **Fix Safe Issues** button.
- **Mesh Data = Object Name** — a new **Individual Steps** button renames each mesh data-block to match its object (`Barrel_01` → mesh `Barrel_01`); shared meshes are skipped.

### Budgets & dashboard (Budget Manager)
- **Asset budgets** — per-asset triangle / material (draw-call) / texture-VRAM limits, a **Budget** category + score, and **computed optimization savings** ("downscale 2 textures → save ~48 MB").
- **Project Health dashboard** — whole-scene totals vs project budget as used / limit / % bars + **Top expensive** assets, in the window and the HTML report.
- **Executive Summary** hero + **thumbnails** in the HTML report.
- **Project presets** — Mobile Low/High, PC Indie, VR.

### Release gate & regressions
- **Release Gate** — a one-glance **RELEASE READY / BLOCKED** verdict (in the dashboard, HTML report, and CLI), blocked by failing assets, import failures, project-budget overruns, or average health below the **Release Health Threshold** (default 85, set per profile).
- **Regression tracking** — a "Since last scan" block comparing project health and triangle / VRAM / material totals against the previous report, plus the assets whose score dropped the most.

### Report & in-scene UX
- **Report history** — every folder scan keeps a dated copy in a `history/` subfolder (Archive toggle), so the folder is a source of truth for trends and comparisons.
- **Compare Reports** — diff any saved report (a history snapshot or a pinned baseline) against the current one: Fixed / New, per-asset score deltas, Most Improved / Worst Regressions — as a self-contained diff page.
- **New vs Fixed** — the report lists which findings were **fixed** and which are **new** since the previous scan (per asset + rule), so teams track progress on what changed instead of re-reading the backlog.
- **HTML report from the scene** — generate the same rich HTML report from an in-scene validation (one card per object), not just from a folder scan. The window has **HTML Scene** + **HTML Folder** buttons.
- **By rule** view in the HTML — actionable findings grouped by rule with counts (*Missing Texture File — 6 assets*), collapsible.
- **Ignored** in the report — an Ignored count (cards + summary), a muted `ignored` tag on muted findings, and an **Ignored** filter that shows only the muted ones.
- **Thumbnail size** — Small / Medium / Large; each asset is isolated during render so the preview shows only that asset.
- **Expand all / Collapse all** + a ▸ chevron so cards clearly read as expandable.
- **Re-check** — re-validate in place (per issue and next to Fix Safe), so you don't scroll back to Run Validation after a manual fix.
- **Show / Hide ignored** in-scene — preview exactly what's muted (scoped to the ticked objects) with a per-finding **✕** un-ignore.
- **Remove All Collision** now respects every configured collider naming convention (`.collision`, `_col`, custom…), not just `UCX_`/`UBX_`.
- **Save Current as Profile…** captures your settings (incl. Asset Type) into the project `profiles.json`; selecting a profile now **applies immediately**.
- **Scene Unit Scale ≠ 1.0** check — flags the hidden footgun behind wrong import sizes.

### Authoring & validation
- **Export LOD Chains (FBX)** — exports a base mesh + its LODs as one grouped FBX renamed `Base_LOD0…_LODn`, so **Unity auto-creates a LOD Group** on import. (Unreal needs a manual *Import Mesh LOD* per level; Godot auto-generates LODs and doesn't need manual chains — the panel shows the right hint per engine.) Works on temp copies; the scene is untouched.
- **Texel density check** (opt-in) — flags assets whose texture pixels-per-metre fall outside a target band, for consistent density across a set.
- **Lightmap UV outside 0–1** — errors when the lightmap channel packs outside the 0–1 square (breaks baking).
- **Set Game Pivot** — one-click origin presets per asset type (Prop / Architecture / Foliage → bottom-center; Weapon / Vehicle → bounds-center).
- **LOD decimate options** — preserve symmetry (with axis) and triangulate LODs in the generator.

### More
- **Save Current as Profile…** — capture the current thresholds, checks, naming, budgets and release settings into your project `profiles.json` as a named profile, straight from the UI (no JSON editing); commit it and the team gets it.
- **glTF / GLB export** — Export Selected now offers **FBX or glTF 2.0** (GLB single-file or separate `.gltf`), for Godot and web pipelines. Same engine preset, cleanup, and budget gate; tangents per engine convention.
- **Material / Texture checks+** — flags a **non-standard surface shader** (custom node group that won't bake to PBR) and **mismatched texture resolutions** within one material (both informational).
- **Safe-only repairs** — the destructive geometry auto-fixes (fill holes, triangulate n-gons, remove loose / zero-area) were removed entirely. Geometry problems are now manual: **Select & Focus** takes you to the exact components and you fix them with Blender's own (undoable) tools.
- **Viewport header button** — an always-visible **Preflight** button in the 3D viewport header that opens the window (turns red when the last scan found errors).
- **Naming templates** + one-click auto-rename (`SM_{Category}_{Name}_{NN}`).
- **Per-object / whole-scene Ignore** (mark intentional → `(ignored)` info).
- **Asset Type** layer (PBR / Lightmap / Procedural / Kitbash-Atlas).
- **Safe-only fixes** — destructive geometry auto-repair removed in favour of Select & Focus.
- **Persistent tabbed N-panel** (docked; buttons don't dismiss it) alongside the window.
- **CI package** (`ci/` — GitHub Action + pre-commit) + shareable rule-packs.

### Asset Preflight window
- One centered, tabbed window — **Presets · Scope · Rules · Scan · Results** — opened from a single **Open Asset Preflight** button. Replaces the old *Validation* and *Fix* panels. Interactive viewport tools (Collision, LOD, Quick Reset, Export, FBX) stay in the N-panel.

### Validation Profiles
- Editable, shareable [`profiles.json`](features/profiles.md) — engine/studio rule bundles (thresholds, checks, naming, collider conventions, per-issue severity overrides). Built-ins: **Unity Mobile/Desktop, Unreal, Godot 4, Marketplace**.
- Point at a **project** `profiles.json` so a whole team validates identically; **Apply** / **Reload** in the Presets tab.

### LOD tools
- **LOD generator** with per-level decimation keep-% and on-screen switch-%, written as a switching policy onto LOD0.
- **Live preview** — drive the visible level from a distance slider or the real viewport camera.
- **LOD validation** — reduction monotonicity, material / UV consistency, bounds deviation, naming/chain gaps, and switch-threshold order.

### Batch scan & HTML report
- Scan a **folder** of exported assets (FBX/glTF/GLB/OBJ) into a self-contained, shareable [HTML report](features/batch-scan.md): health scores, export-risk level, **duplicate-geometry** detection, and a quality trend. **Delta scan** skips unchanged files.

### Headless CLI
- [`preflight_cli.py`](features/cli.md) runs the same scan via `blender --background` and exits non-zero on failures — a drop-in **CI gate**.

### Collider recognition
- Broad, **configurable** [collider naming](reference/collider-naming.md) (Unreal `UCX_/UBX_/USP_/UCP_`, `*.collision`, `*_col`, Godot `-colonly`, …), matched to the render mesh — no more false errors on imported colliders.

### Select & Focus
- Now **frames the 3D viewport** on the selected problem and enables the face-orientation overlay for flipped faces.

### Report schema
- Scene report bumped to **1.1** (additive: `scores.lod`, profile/collider config keys); batch report `batch-1.0`. See [Report Schema](reference/report-schema.md).

---

## [1.0.0]

First public release.

### Export
- FBX export with engine presets for **Unity**, **Unreal Engine**, **Godot**, and **Custom** axes.
- One FBX per selected mesh; any matching `UCX_`/`UBX_` collision meshes are included automatically.
- Auto-cleanup toggles applied to a temporary copy: **Apply Transforms**, **Apply Modifiers**, **Triangulate**, **Collect Textures**, and **Rename Textures** (`AssetName_TextureType`, detected from the material node graph).
- Export prefix setting (default `SM_`) and both relative (`//`) and absolute export paths, with a clear error when a relative path is used from an unsaved `.blend`.
- Header progress bar during batch export and a summary popup showing exported, warnings, and any failed assets.

### Better FBX integration
- Auto-detects [Better FBX Importer & Exporter](panels/output.md) at runtime — no manual toggle. When installed, a single-object export opens its full dialog and batch export uses it silently with engine-matched axis presets.
- **Import FBX…** button opens Better FBX's import dialog when the addon is installed.

### Geometry prep
- **Quick Reset** — pivot to bottom, origin to world zero, apply rotation and scale in one click.
- **Individual Steps** — pivot bottom/center, apply transforms, apply modifiers, triangulate, add lightmap UV.
- **Preview / Isolate** — modal browser that steps through scene meshes (mouse wheel / arrow keys) and isolates each as **Mesh only**, **Mesh + Collider**, or **Collider only** (`M` to cycle), plus a **Restore All** button.

### Collision
- Generate `UCX_` convex hull and `UBX_` bounding box collision meshes with a configurable decimate ratio.
- Automatic collider workflow by engine: **Unity** parents colliders to the render mesh, **Unreal** organises them into collections, other engines place them at scene root.
- **Dissolve Collections** reverses collection grouping without deleting any objects.

### Validation
- Asset validation across transform, geometry, normals, UV (incl. **overlap** and **padding**),
  materials, textures, naming and collision. Runs on the **evaluated** mesh when export applies modifiers.
- Dual-metric scoring: pooled **export gate** (`EXPORT READY` / blocking) plus **scene quality**
  (mean per-asset, for ranking / batch triage); per-category and per-asset scores.
- Engine + **Marketplace** severity presets.
- **Report UI**: collapsible per-category sections, impact explanations, fix-confidence
  (Safe / Destructive / Manual), and **Select Problem** with shader-graph & UV-editor deep-link.
- **Asset Report export** (JSON / CSV): versioned schema 1.0, deterministic content hash.
- **Batch validation**: Selection / Collection / Scene.
- Auto-fixes, including a non-destructive **Fix Safe Issues**.

### Fix
- An individual fix operator for each validation category plus **Fix All Issues**; the Validation panel refreshes automatically after every fix.

### Viewport helpers
- **Forward Axis gizmo** — viewport arrow showing the export forward direction, updates with the engine preset.
- **Scale Reference** — 1 m cube and 1.8 m human-height reference box.

### Packaging
- N-Panel UI in the **GameExport** tab (`View3D > Sidebar > GameExport`).
- GPL-3.0 license included; `doc_url` and `tracker_url` set in `bl_info` so links appear in Blender's Addon Preferences.
