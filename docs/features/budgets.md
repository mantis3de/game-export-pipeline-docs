# Budgets & Project Dashboard

Keep every asset — and the whole level — inside your production budget. This is the
producer's view: not "is this asset broken?" but "are we within our triangle, draw-call
and texture-memory budget?".

---

## Per-asset budgets

Every profile carries a budget for the metrics that actually matter at runtime:

| Metric | Why it matters |
|---|---|
| **Triangles** | GPU cost & memory |
| **Materials** | ≈ draw calls — batching / mobile performance |
| **Texture VRAM** | streaming & memory budget (estimated, uncompressed upper bound) |

When an asset is over budget you get a **Budget** issue with the exact number, e.g.
`~96 MB textures over the 64 MB budget`, plus a **computable suggestion** — see below.

Edit the limits in **Asset Preflight → Rules → Asset Budgets**, or per profile in
`profiles.json` (`budget` block). The **Budget** category has its own score.

---

## Project Health dashboard

After a scan, the **Scan & Results** tab shows **Project Health** — the level's
totals against the project budget, as used / limit / % bars:

```
Triangles    ██████████████░░░░  78%   1.56M / 2.0M
Texture VRAM █████████████████░  82%   3.36 / 4.0 GB
Materials    ████████░░░░░░░░░░  41%   124 / 300
```

Plus **Top by triangles** — the most expensive assets, so you know where to optimise
first. This is the screen that turns a validator into a budget manager.

Project budgets are per profile (`budget.scene_max_*`) and editable in Rules.

---

## Computable optimization suggestions

Over-budget findings come with a **calculated** fix, not vague advice:

- Texture VRAM: *"downscale 2 texture(s) to 2048px → save ~48 MB"*
- Materials: *"merge 2 material(s) / use an atlas → fewer draw calls"*
- Triangles: *"decimate or build LODs to fit the budget"*

The savings are computed from the actual texture sizes, so the number is real.

---

## Executive Summary (HTML report)

The batch [HTML report](batch-scan.md) opens with an **Executive Summary** hero — the
screen a lead forwards on Slack / Jira:

- big **Project Health** score + **Export Risk Level**,
- asset counts (pass / warn / fail / import-fail),
- **budget % bars** (Triangles / Texture VRAM / Materials).

Below it, every asset card shows its tris / materials / ~VRAM and a **thumbnail**.

---

## Project presets

Ready budget profiles for common targets — pick one and Apply:

`Unity Mobile/Desktop`, `Unreal Desktop`, `Godot 4`, `Marketplace`,
**`Mobile Low-End`**, **`Mobile High-End`**, **`PC Indie`**, **`VR (90 fps)`**.

Copy and tune them in `profiles.json`. → [Profiles Reference](../reference/profiles-reference.md)
