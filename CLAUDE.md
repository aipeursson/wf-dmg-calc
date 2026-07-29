# WF Damage Calculator

## Screenshot scans

Enemy screenshots show an Attributes Comparison popup with **two sides**:
- **Left side**: jackclown's stats (the user's own character)
- **Right side**: the enemy's stats

When scanning a folder of screenshots, extract and save jackclown's values **once** (they repeat identically across all enemies in the same war week). Store them separately from the enemy array — e.g. as a top-level `"player"` object in the output JSON.

## Output JSON structure

```json
{
  "player": {
    "name": "jackclown",
    "bp": ...,
    "mg_atk": ...,
    ...all stat fields...
  },
  "enemies": [
    { "name": "...", "bp": ..., ... },
    ...
  ]
}
```

## Enemy data schema (per enemy)

Fields to extract for each enemy:

**Basic:** `bp`, `mg_atk`, `wg_atk`, `ms_atk`, `hp`

**Battle attrs 1:** `crit`, `dodge`, `elec_enh`, `burn_enh`, `reflect_enh`

**Battle attrs 2:** `crit_res`, `hit`, `elec_dec`, `burn_dec`, `reflect_dec`

**Special attrs 1:** `pdb`, `pd`, `edb`, `ed`, `fid_inc`, `fid_dec`

**Special attrs 2:** `sentinel_enh`, `sentinel_dec`, `heal_enh`, `heal_dec`, `dmg_inc`, `dmg_red`

**PvP / aerial:** `pvp_inc`, `pvp_red`, `aerial_inc`, `aerial_red`

**Per-weapon crit (added by wf-crit-scan):** `mg_crit` *(only if visible in screenshots)*, `mg_crit_res`, `wg_crit`, `wg_crit_res`, `ms_crit`, `ms_crit_res`, `mg_crit_dmg`, `mg_crit_dmg_res`, `wg_crit_dmg`, `wg_crit_dmg_res`, `ms_crit_dmg`, `ms_crit_dmg_res`

**Meta:** `name`, `label` (grid position, filled in later from battle board), `drones` (3 slots, filled in later)

Each enemy has ~9 screenshots: 6 core stat screens + 3 per-weapon detail screens. The per-weapon detail screens are handled by the **wf-crit-scan** agent (see `.claude/agents/wf-crit-scan.md`).

## Calculator structure

Single HTML file (`index.html`) deployed to GitHub Pages.

- `WEEKS` array: one entry per war week `{id, label}`
- `currentWeek`: defaults to last entry in WEEKS
- `WEEKS_SEED[weekId]`: array of enemy objects for that week, deep-cloned on week switch
- Week ID format: `wf` + `MMDDYY` (e.g. `wf072626` = Jul 26 2026)
- After editing `index.html`, copy to `../wf_calc.html` then commit + push from the `wf-dmg-calc/` git repo
