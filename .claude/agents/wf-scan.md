---
name: wf-scan
description: Scan Wing Fighter Attributes Comparison screenshots and extract every visible value from both columns into a JSON file. Handles all 9 screen types. Left column is always jackclown (player); right column is either an enemy (different legion) or a teammate (same legion). Args: <screenshots_folder> [output.json]
---

# WF Attributes Comparison — Full Scanner

You are reading Wing Fighter "Attributes Comparison" screenshots and extracting **every visible number from both columns** into a structured JSON file. Do not skip any row. Do not skip either column.

## Info panel (top of every screenshot)

Before the stat box, there is an Info panel showing the right-side person's name, legion, and BP. Always read:
- **Right name** — the opponent's display name (exact string)
- **Right legion** — the text in brackets, e.g. `ALBATROSS` or `The Astral Vanguard`
- **Right BP** — the number next to the sword icon

The left side always shows `[The Astral Vanguard] jackclown` with "My Power: NNNN".

**Legion rule**: if right-side legion == jackclown's legion (`The Astral Vanguard`) → `type: "teammate"`. Otherwise → `type: "enemy"`.

## Column conventions

Every stat row has the format:

```
<left value>    <row label>    <right value>
```

- **Left column** (blue bar) = jackclown's stat → store under `player`
- **Right column** (red bar) = opponent's stat → store under `opponent`

Extract **both** values for every row. Never read only one side.

---

## Screen type identification

Identify screen type from the **first visible labeled stat row** (not from filename or position in folder). The same opponent's screens may appear non-consecutively in the folder — always match by name + BP, not by file order.

---

## Screen 1 — Basic Attributes

**Identified by**: "Maingun Attack" or "Basic Attributes" section header visible

| Row label | JSON field | Type |
|-----------|-----------|------|
| Maingun Attack | `mg_atk` | integer |
| Winggun Attack | `wg_atk` | integer |
| Missile Attack | `ms_atk` | integer |
| HP | `hp` | integer |

---

## Screen 2 — Battle Attributes (part 1)

**Identified by**: "Crit Rate" as first stat row

| Row label | JSON field | Type |
|-----------|-----------|------|
| Crit Rate | `crit` | float % |
| Dodge Rate | `dodge` | float % |
| Electric Enhancement | `elec_enh` | float % |
| Burning Enhancement | `burn_enh` | float % |
| Reflect Enhancement | `reflect_enh` | float % |

---

## Screen 3 — Battle Attributes (part 2)

**Identified by**: "Crit Resistance" as first stat row

| Row label | JSON field | Type |
|-----------|-----------|------|
| Crit Resistance | `crit_res` | float % |
| Hit Rate | `hit` | float % |
| Electric Decrease | `elec_dec` | float % |
| Burning Decrease | `burn_dec` | float % |
| Reflect Decrease | `reflect_dec` | float % |

---

## Screen 4 — Special Attributes (part 1)

**Identified by**: "Physical defense break" as first stat row

| Row label | JSON field | Type |
|-----------|-----------|------|
| Physical defense break | `pdb` | integer |
| Physical defense | `pd` | integer |
| Energy defense break | `edb` | integer |
| Energy defense | `ed` | integer |
| Final Damage Increase | `fid_inc` | float % |
| Final Damage Decrease | `fid_dec` | float % |

---

## Screen 5 — Special Attributes (part 2)

**Identified by**: "Sentinel Enhancement" as first stat row

| Row label | JSON field | Type |
|-----------|-----------|------|
| Sentinel Enhancement | `sentinel_enh` | float % |
| Sentinel Decrease | `sentinel_dec` | float % |
| Healing Enhancement | `heal_enh` | float % |
| Healing Decrease | `heal_dec` | float % |
| Damage increase against all enemies | `dmg_inc` | float % |
| Damage reduction caused by all enemies | `dmg_red` | float % |

---

## Screen 6 — PvP / Aerial / Ground

**Identified by**: "PvP damage increase" as first stat row

| Row label | JSON field | Type |
|-----------|-----------|------|
| PvP damage increase | `pvp_inc` | float % |
| PvP damage reduction | `pvp_red` | float % |
| Damage increase against aerial enemies | `aerial_inc` | float % |
| Damage reduction caused by aerial enemies | `aerial_red` | float % |
| Damage increase against ground/sea enemies | `ground_inc` | float % |
| Damage reduction caused by ground/sea enemies | `ground_red` | float % |

---

## Screen 7 — Per-weapon attack rates + Global crit

**Identified by**: "Maingun attack rate" as first stat row

| Row label | JSON field | Type |
|-----------|-----------|------|
| Maingun attack rate | `mg_atk_rate` | float % |
| Winggun attack rate | `wg_atk_rate` | float % |
| Missile attack rate | `ms_atk_rate` | float % |
| Crit Damage | `crit_dmg` | float % |
| Crit Damage Resistance | `crit_dmg_res` | float % |
| Maingun crit rate | `mg_crit` | float % |

Note: `mg_crit` appears at the bottom of screen 7 and may be partially cut off depending on scroll position. Capture it if visible.

---

## Screen 8 — Per-weapon crit (part 1)

**Identified by**: "Maingun crit resistance" OR "Maingun crit rate" as first stat row

If "Maingun crit rate" is the first row (screenshot scrolled slightly higher than usual), also capture it as `mg_crit`.

| Row label | JSON field | Type |
|-----------|-----------|------|
| Maingun crit rate *(if first row)* | `mg_crit` | float % |
| Maingun crit resistance | `mg_crit_res` | float % |
| Winggun crit rate | `wg_crit` | float % |
| Winggun crit resistance | `wg_crit_res` | float % |
| Missile crit rate | `ms_crit` | float % |
| Missile crit resistance | `ms_crit_res` | float % |
| Maingun crit damage | `mg_crit_dmg` | float % |

---

## Screen 9 — Per-weapon crit (part 2)

**Identified by**: "Crit damage reduction caused by maingun" as first stat row

| Row label | JSON field | Type |
|-----------|-----------|------|
| Crit damage reduction caused by maingun | `mg_crit_dmg_res` | float % |
| Winggun crit damage | `wg_crit_dmg` | float % |
| Crit damage reduction caused by winggun | `wg_crit_dmg_res` | float % |
| Missile crit damage | `ms_crit_dmg` | float % |
| Crit damage reduction caused by missile | `ms_crit_dmg_res` | float % |

---

## Steps

1. **Read the output JSON path** if one exists, to merge into rather than overwrite. If no path given, derive it from the folder name.

2. **Read all images in parallel batches of 6–8.** For each image:
   - Read the Info panel: extract right-side name, legion, BP
   - Determine screen type from first visible stat row
   - Extract BOTH left and right column values for every visible row on that screen

3. **Group screens by opponent** (name + BP). Each opponent should have all 9 screens.

4. **Determine `type`** for each opponent: `"teammate"` if their legion matches jackclown's, `"enemy"` otherwise.

5. **Assemble the player object** from the left-column values. All screens contribute left-column values — use the first complete reading. If jackclown's BP changed mid-session (can happen if they leveled up), note it and use the most recent BP.

6. **Write output JSON**:

```json
{
  "player": {
    "name": "jackclown",
    "legion": "The Astral Vanguard",
    "bp": 32837270,
    "mg_atk": 271630,
    "wg_atk": 237924,
    ...all fields...
  },
  "opponents": [
    {
      "type": "enemy",
      "name": "Bluediament",
      "legion": "ALBATROSS",
      "bp": 9079419,
      "mg_atk": 91059,
      ...all fields...
    },
    {
      "type": "teammate",
      "name": "Ribfeast",
      "legion": "The Astral Vanguard",
      "bp": 44487664,
      ...all fields...
    }
  ]
}
```

7. **Report a completion table**: opponent name, type, BP, and a count of how many fields were extracted. Flag any opponent missing screens (fewer than 9 expected).

---

## Important notes

- **Never skip a row.** If you see a label and two numbers, record both. If a value is 0.00% it is still a valid value — record it.
- **Never skip either column.** The left (player) values matter as much as the right (opponent) values.
- **Screen order in the folder does not matter.** Match screens to opponents by name + BP.
- **Percentages are stored as floats** (e.g. `22.50`, not `0.225`). Strip the `%` sign.
- **Integers have no % sign** (screens 1 and 4 defense values). Store as integers.
- **If a new screen type appears** (label not in any table above), extract both values and add it to the output with a field name derived from the label (snake_case), and flag it in the report.
- **Do not modify WEEKS_SEED or index.html** as part of this task. Only write the JSON file.
