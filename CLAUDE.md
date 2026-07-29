# WF Damage Calculator

## Screenshot scans

Attributes Comparison screenshots show a popup with **two columns**:
- **Left column** (blue bar): jackclown's stats
- **Right column** (red bar): the opponent's stats — either an enemy (different legion) or a teammate (same legion)

Extract **both columns** from every screen. Store jackclown's values in a top-level `"player"` object; store opponents in an `"opponents"` array with `type: "enemy"` or `type: "teammate"`. Use the **wf-scan** agent (`.claude/agents/wf-scan.md`) for all screenshot ingestion.

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

## Opponent data schema

Each opponent (enemy or teammate) has 9 screenshot screens. Use the **wf-scan** agent (`.claude/agents/wf-scan.md`) to extract all fields from both columns. The complete field set:

**Screen 1 — Basic (integers):** `bp`, `mg_atk`, `wg_atk`, `ms_atk`, `hp`

**Screen 2 — Battle 1 (%):** `crit`, `dodge`, `elec_enh`, `burn_enh`, `reflect_enh`

**Screen 3 — Battle 2 (%):** `crit_res`, `hit`, `elec_dec`, `burn_dec`, `reflect_dec`

**Screen 4 — Special 1 (integers + %):** `pdb`, `pd`, `edb`, `ed`, `fid_inc`, `fid_dec`

**Screen 5 — Special 2 (%):** `sentinel_enh`, `sentinel_dec`, `heal_enh`, `heal_dec`, `dmg_inc`, `dmg_red`

**Screen 6 — PvP/Aerial/Ground (%):** `pvp_inc`, `pvp_red`, `aerial_inc`, `aerial_red`, `ground_inc`, `ground_red`

**Screen 7 — Per-weapon attack rates + global crit (%):** `mg_atk_rate`, `wg_atk_rate`, `ms_atk_rate`, `crit_dmg`, `crit_dmg_res`, `mg_crit`

**Screen 8 — Per-weapon crit 1 (%):** `mg_crit_res`, `wg_crit`, `wg_crit_res`, `ms_crit`, `ms_crit_res`, `mg_crit_dmg`

**Screen 9 — Per-weapon crit 2 (%):** `mg_crit_dmg_res`, `wg_crit_dmg`, `wg_crit_dmg_res`, `ms_crit_dmg`, `ms_crit_dmg_res`

**Meta:** `name`, `legion`, `type` (`"enemy"` or `"teammate"`), `label` (grid position, filled later), `drones` (3 slots, filled later)

## Calculator structure

Single HTML file (`index.html`) deployed to GitHub Pages.

- `WEEKS` array: one entry per war week `{id, label}`
- `currentWeek`: defaults to last entry in WEEKS
- `WEEKS_SEED[weekId]`: array of enemy objects for that week, deep-cloned on week switch
- Week ID format: `wf` + `MMDDYY` (e.g. `wf072626` = Jul 26 2026)
- After editing `index.html`, copy to `../wf_calc.html` then commit + push from the `wf-dmg-calc/` git repo
