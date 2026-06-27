# Release Gate & Regression Tracking

The Release Gate turns a folder scan into a single ship / don't-ship verdict, and
regression tracking shows what got worse since the previous scan. Together they
let a producer or solo dev answer "is this build safe to hand to the engine?"
without reading every asset card.

## Release Gate

After a batch scan the report carries a **RELEASE READY** or **BLOCKED** verdict.
It appears in three places:

- the **Project Health** line at the top of the Asset Validator dashboard,
- a coloured badge above the Executive Summary in the **HTML report**,
- a `release:` line in the **headless CLI** output.

A build is **BLOCKED** when any of these is true:

- one or more assets **fail** (contain an error),
- one or more files **failed to import**,
- a **project budget** is exceeded (total triangles, texture VRAM, or materials
  over the project limit),
- average **project health** is below the **Release Health Threshold**.

When blocked, the verdict lists the concrete reasons (e.g. `1 failing asset(s)`,
`health 77 < 85`, `triangle budget exceeded`) so you know exactly what to fix.

### Release Health Threshold

The threshold is the minimum acceptable average health (0–100, default **85**).
Set it per profile under `release.min_health` in
[`profiles.json`](profiles.md), or adjust it for the current scene in the window.
A stricter team raises it; a prototype can lower it.

## Regression Tracking

When a previous report exists in the output folder, the new report adds a
**Since last scan** block comparing this scan to the last one:

- **Project Health** delta (green when it improved, red when it dropped),
- **Triangles**, **Texture VRAM**, and **Materials** totals as a percentage
  change — an increase is shown red (it costs budget), a decrease green,
- **Worst regressions** — the assets whose score dropped the most, with their
  previous and current score.

This makes creeping budget growth and quietly degrading assets visible across
iterations, instead of only seeing a single snapshot.

### New vs Fixed

Below the totals, a **New vs Fixed** breakdown lists the actionable findings
(ERROR / WARNING) that changed since the previous scan — **FIXED** (resolved) and
**NEW** (appeared), each as `asset — rule`, with a `X fixed · Y new` header. So a
team can focus on what changed this iteration instead of re-reading the whole
backlog. Compared as `(asset, rule)` pairs against the previous report.

## CLI

The headless gate prints the verdict and exits non-zero when blocked, so a build
server or pre-publish hook can stop a bad build:

```
[preflight] 12 files — 10 pass / 1 warn / 1 fail · avg health 84 · Export Not Recommended
[preflight] release: BLOCKED — 1 failing asset(s); health 84 < 85
```

See [Headless CLI](cli.md) for the full command and exit codes.
