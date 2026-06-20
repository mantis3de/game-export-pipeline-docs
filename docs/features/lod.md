# LOD Tools

Generate Level-of-Detail chains, set when each level appears on screen, preview the switching live in the viewport, and validate the result — all from the **LOD Tools** panel in the GameExport sidebar.

---

## Two independent settings

LOD has two knobs that are easy to confuse — they're different things:

- **Decimation — keep %** — how many triangles each LOD keeps (LOD1 50 % / LOD2 25 % / LOD3 10 %). This is the geometry reduction.
- **Switch @ screen %** — at what on-screen size each level takes over (LOD1 50 % / LOD2 25 % / LOD3 10 %). This is the switching policy. **Values must descend** (the object gets smaller on screen as it recedes).

There are no presets — you set both directly, defaulting to 50 / 25 / 10. The **Reference FOV** is the vertical field of view the switch percentages are authored against.

---

## Generating LODs

1. Select a **clean** base mesh (LOD0).
2. Set the keep % and switch % and the **Levels** count (1–3).
3. Click **Generate LODs**.

This creates `Name_LOD1`, `Name_LOD2`, … beside the base and writes a **switching policy** onto LOD0 (visible in the *APPLIED on '…'* line). Re-running tops up the chain rather than clobbering it.

!!! warning "Fix the base first"
    A base mesh with **errors** won't generate LODs — decimating a broken mesh just copies the defect into every level. Fix LOD0, or untick **Validate Base First** to force.

**Apply / Refresh Policy** re-writes the policy onto selected chains *without* regenerating geometry — use it after changing the % or FOV.

---

## Preview (runtime emulator)

The preview shows exactly which level the engine would display at a given camera distance, using the same screen-size math the policy stores.

1. Turn on **Preview LODs**.
2. Drag **Distance (m)** — or click **Live (from viewport)** and orbit / zoom. The Live button is blue while running.
3. The readout shows e.g. `Chair: LOD2 @ 27 m / 14 % screen`.

While **Preview** is on, editing the switch % updates the view **live** — no need to click Apply. Turning preview off restores the original visibility of every level.

→ The full screen-size model is documented in `LOD_SPEC.md` in the addon.

---

## LOD validation

When a chain exists, **Run Validation** adds an **LOD** category:

| Check | Severity | Meaning |
|---|---|---|
| **LOD Not Reduced** | error | a level has ≥ as many tris as the previous one (a duplicate that was never decimated) |
| **LOD Lost UV Channel** | error | a level dropped a UV layer present on LOD0 |
| **LOD Material Mismatch** | warning | a level uses different material slots than LOD0 (materials pop on swap) |
| **LOD Bounds Deviation** | warning | a level's bounding box differs too much from the base (silhouette popping) |
| **LOD Naming / Chain Gap** | warning | broken `_LODn` numbering |
| **LOD Threshold Order / Cull** | error / warning | switch % not strictly descending, or cull above the last switch |
| **LOD Detail / Switch Mismatch** | warning | an aggressively decimated level activates while the object is still large on screen |

!!! note "Nanite users"
    UE5 Nanite often makes manual LODs unnecessary. If you don't use LODs, simply don't generate them — the LOD category stays at 100.
