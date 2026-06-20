# Naming Templates

Enforce your studio's naming convention and apply it in one click. A consistent
`SM_Category_Name_NN` across the whole team means assets are easy to find, sort,
and feed to pipeline tooling.

---

## The template

Set it in **Asset Preflight → Presets → Naming Template**, e.g.:

```
SM_{Category}_{Name}_{NN}
```

Placeholders:

| Token | Meaning |
|---|---|
| `{Name}` | the asset's base name |
| `{Category}` | a project category (Props, Env, Char…) |
| `{N}` / `{NN}` / `{NNN}` | incrementing index, zero-padded to the number of N's |

Leave the template empty to disable the convention check.

---

## Validating against the convention

When a template is set, any object whose name doesn't match it is flagged with an
**Off Naming Convention** issue (Naming category). Colliders are skipped so the
`UCX_`/`*.collision` pairing isn't broken.

---

## Auto-rename

With a template set, two buttons appear:

- **Rename Selected** — rename the selected mesh objects to the convention.
- **Whole Scene** — rename every render mesh in the scene.

Set the **Category** and **Start** index first. Renaming is **idempotent**: if a name
already matches the template, `{Name}` is extracted and kept, so re-applying doesn't
produce `SM_Props_SM_Props_Barrel_01_01`. Names are kept unique automatically.

---

## Share it with the team

Put the template in your project `profiles.json` under `naming.template`, so the
whole team — and CI — uses the same convention:

```jsonc
"naming": { "strict": true, "template": "SM_{Category}_{Name}_{NN}" }
```

→ [Validation Profiles](profiles.md)
