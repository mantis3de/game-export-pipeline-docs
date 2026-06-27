# Workflow — CI Gate

Block broken assets automatically, before they reach `main`.

The headless CLI runs the same scan as the window and **exits non-zero** when assets fail — so a build server or pre-merge hook can fail the job.

---

## Run it

From the addon folder:

```bash
blender --background --python preflight_cli.py -- \
    --folder ./Exports \
    --profiles ./preflight/profiles.json \
    --profile studio_unity \
    --out ./reports \
    --fail-on error
echo $?     # 0 = pass · 1 = blocked · 2 = usage error
```

✓ Writes `asset_preflight_report.html` + `.json`, prints a summary, lists every `FAIL` / import failure.

---

## GitHub Actions

```yaml
name: Asset Validator
on: [push, pull_request]
jobs:
  preflight:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Validate exported assets
        run: |
          blender --background \
            --python addons/game_export_pipeline/preflight_cli.py -- \
            --folder ./Exports \
            --profiles ./preflight/profiles.json \
            --profile studio_unity \
            --fail-on error
      - uses: actions/upload-artifact@v4
        if: always()
        with:
          name: preflight-report
          path: ./Exports/asset_preflight_report.html
```

The job goes red the moment someone commits a broken asset; the HTML report is uploaded as an artifact either way.

---

## Tuning the gate

- **`--fail-on error`** (default) — block on errors / import failures only; warnings pass.
- **`--fail-on warning`** — strict: warnings block too.
- **`--profile` / `--profiles`** — use the same shared ruleset as the team, so CI and artists agree.
- **`--no-delta`** — force a full re-scan (CI usually wants this for a clean checkout).

→ See [Headless CLI](../features/cli.md) for all options and [Report Schema](../reference/report-schema.md) for the JSON.
