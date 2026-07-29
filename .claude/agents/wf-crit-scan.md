---
name: wf-crit-scan
description: Scan Wing Fighter per-weapon crit screenshots and merge extracted values into an enemies JSON file. Use when the user has a folder of Attributes Comparison crit screenshots and wants to add mg_crit_res, wg_crit, wg_crit_res, ms_crit, ms_crit_res, mg_crit_dmg, mg_crit_dmg_res, wg_crit_dmg, wg_crit_dmg_res, ms_crit_dmg, ms_crit_dmg_res fields to their enemies JSON. Args: <screenshots_folder> <enemies.json>
---

# WF Crit Screenshot Scanner

You are scanning Wing Fighter "Attributes Comparison" screenshots that show per-weapon crit stats and merging the extracted values into an existing enemies JSON file.

## Overview

Each enemy requires **two screenshots** (though they may not be consecutive in the folder — match by enemy name + BP):

- **Screen 1** — starts with "Maingun crit resistance" (or occasionally "Maingun crit rate" if scrolled slightly higher). Visible rows: mg_crit_res, wg_crit, wg_crit_res, ms_crit, ms_crit_res, mg_crit_dmg. If "Maingun crit rate" is the first row, also capture mg_crit.
- **Screen 2** — starts with "Crit damage reduction caused by maingun". Visible rows: mg_crit_dmg_res, wg_crit_dmg, wg_crit_dmg_res, ms_crit_dmg, ms_crit_dmg_res.

## Screen layout reference

**Screen 1 row order (right/enemy column):**
| Row label | JSON field |
|-----------|-----------|
| Maingun crit rate *(if visible)* | `mg_crit` |
| Maingun crit resistance | `mg_crit_res` |
| Winggun crit rate | `wg_crit` |
| Winggun crit resistance | `wg_crit_res` |
| Missile crit rate | `ms_crit` |
| Missile crit resistance | `ms_crit_res` |
| Maingun crit damage | `mg_crit_dmg` |

**Screen 2 row order (right/enemy column):**
| Row label | JSON field |
|-----------|-----------|
| Crit damage reduction caused by maingun | `mg_crit_dmg_res` |
| Winggun crit damage | `wg_crit_dmg` |
| Crit damage reduction caused by winggun | `wg_crit_dmg_res` |
| Missile crit damage | `ms_crit_dmg` |
| Crit damage reduction caused by missile | `ms_crit_dmg_res` |

"Attack against aerial enemies" and any rows below it are **ignored**.

## Column conventions

- **Left column (blue bar)**: jackclown's stats — ignore for enemy extraction
- **Right column (red bar)**: enemy's stats — extract these

The Info panel at the top of each screenshot shows the enemy's name and BP (power). Always match to the JSON entry by **name** (exact string match). The BP shown on screen may differ by ±1–2 from the JSON due to rounding — this is expected.

## Steps

1. **Read the target enemies JSON** to get the list of enemy names you need to match.

2. **Read all PNG images in the screenshots folder in parallel batches** (6–8 at a time). For each image:
   - Read the Info panel: extract enemy name and BP
   - Determine screen type (1 or 2) from the first visible stat row
   - Extract all right-column values for that screen type

3. **Group screens by enemy name**. Each enemy should have exactly one screen 1 and one screen 2. If an enemy has both, combine the fields. If only one screen is present, record what's available and note the gap.

4. **Merge into the enemies JSON**: for each enemy in the JSON that has screenshot data, add/overwrite the crit fields. Preserve all existing fields (bp, mg_atk, crit, drones, etc.). Do not remove or modify fields that aren't in the screenshots.

5. **Write the updated JSON** back to the same path.

6. **Report a summary table**: enemy name → which fields were added, and flag any enemies in the JSON that had no matching screenshots.

## Important notes

- Screenshots may NOT be in consecutive order for the same enemy. The user may have captured screen 2 for enemy A after doing both screens for enemy B. Always match by name, not by file order.
- Some lower-BP or newer players will have fewer per-weapon bonuses invested — values near 0% are valid, not scan errors.
- Do NOT modify the WEEKS_SEED in index.html as part of this task — that is a separate step. Only update the JSON file.
- All values are percentages stored as floats (e.g. `22.50`, not `0.225`).
