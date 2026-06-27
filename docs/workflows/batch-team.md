# Workflow — Batch + Team Report

The lead's loop: check a whole folder of exports and share one report with the team.

1. **▶ Export** your assets to a folder (FBX / glTF / GLB / OBJ).
2. **▶ Set the scope.** Asset Validator → **Scope** → **Batch folder** = that folder, **Output** = where the report goes. Toggle **Subfolders** / **Delta**.
3. **▶ Scan.** **Scan** tab → **Scan Folder**. ✓ Each file is imported into a throwaway scene, validated with the active profile, removed. Import failures are flagged, not fatal.
4. **▶ Open** the report → **Open HTML Report**.
5. **▶ Share.** The `.html` is one self-contained file (no internet, no assets needed) — drop it in Slack / Drive / email. The `.json` beside it is for tooling/CI.

✓ The report shows summary cards, an Export Risk Level, a health trend, a **duplicate-geometry banner**, and per-file cards you can filter / sort / search.

---

## Share one ruleset with the team

So everyone validates identically, not "works on my machine":

1. **▶ Copy** the addon's `profiles.json` into your project repo (e.g. `Project/preflight/profiles.json`) and tune it.
2. **▶ Point** the addon at it: Presets → **Rules file** → **Reload Profiles**.
3. **▶ Commit** it. Teammates pull and point at the same file.

⚠ **Rules file** = the rules you load (**input**). **Export Report** / the HTML = the results (**output**). Two different files.

---

## Delta scans

With **Delta** on, unchanged files (by modification time + size) are skipped and reused from the last run — repeat scans of a large library are near-instant. Edit one asset, re-scan, only that one re-validates.

→ Automate this as a gate in CI: [CI Gate](ci-gate.md).
