# Headless CLI (CI Gate)

Run a folder scan without the GUI and **exit non-zero when assets fail**, so a build server or pre-publish hook can block bad assets automatically. It uses the same scan core and the same `profiles.json` as the window — CI and artists validate identically.

---

## Usage

Run from the addon folder (so `import core` resolves):

```bash
blender --background --python preflight_cli.py -- \
    --folder ./exports --profile unity_mobile --out ./reports
echo $?     # 0 = clean enough · 1 = blocked · 2 = usage/runtime error
```

It writes the same **HTML + JSON** report as the GUI batch scan, prints a one-line summary, and lists every `FAIL` / import failure.

---

## Options

| Flag | Default | Meaning |
|---|---|---|
| `--folder` | *(required)* | Folder of assets to scan |
| `--out` | = `--folder` | Where to write the report |
| `--profile` | none | Profile id from `profiles.json` |
| `--profiles` | none | Path to a **project** `profiles.json` |
| `--no-recursive` | off | Don't scan subfolders |
| `--no-delta` | off | Re-scan every file |
| `--fail-on` | `error` | Exit non-zero on this level or worse (`error` or `warning`) |
| `--quiet` | off | Summary line only |

---

## Exit codes (the gate)

- **0** — no failures (or only warnings, with the default `--fail-on error`).
- **1** — at least one `FAIL` or import failure — *blocked*. With `--fail-on warning`, warnings also block.
- **2** — usage / runtime error (bad folder, no files).

The run also prints a **release verdict** line, e.g. `[preflight] release: BLOCKED — 1 failing asset(s); health 84 < 85`, summarising the [release gate](release-gate.md). The same verdict and the regression comparison are written into the JSON / HTML report.

---

## CI example (GitHub Actions)

```yaml
- name: Asset preflight
  run: |
    blender --background --python addons/game_export_pipeline/preflight_cli.py -- \
      --folder ./Exports \
      --profiles ./preflight/profiles.json \
      --profile studio_unity \
      --fail-on error
```

The job goes red the moment someone commits a broken asset. Because the JSON report is **content-hashed and deterministic**, it's also diffable across runs — you can see exactly what changed.

→ Share `profiles.json` in your repo: [Validation Profiles](profiles.md).
