You are helping the DM build a new beastiary entry for The Wheat Sea campaign. This is a logistics/formatting task — you will structure the DM's creative decisions into the correct JSON schema. Do not invent lore, names, flavor text, or mechanical choices. Ask, then record.

## Phase 1: Gather Information

Enter plan mode and ask the DM the following questions in a single message. Wait for answers before proceeding.

**Ask all of these at once:**

1. **Name & Role** — What is the creature's name? What is its combat role? (Leader / Minion / Mob / Solo / Support)
2. **Type & Size** — Creature type (Humanoid, Beast, Undead, Fiend, etc.) and size (Tiny / Small / Medium / Large / Huge / Gargantuan). For Mobs, include grid footprint (e.g. "Large (3x3x1)").
3. **Core Stats** — Approximate CR/challenge rating, AC, HP (and hit dice if known), speed, and morale score. (Morale can be a number 1–20, "Does not flee", or a conditional description.)
4. **Ability Scores** — Either give the six scores directly, or describe the creature's physical/mental profile and I will suggest scores for your approval.
5. **Combat Actions** — What attacks or abilities does it use on its turn? Include weapon names, whether melee or ranged, rough damage, and any special effects.
6. **Special Mechanics** — Does it have any of the following? (Answer yes/no/describe for each)
   - Bonus actions
   - Triggered actions (reactions or "when X happens" effects)
   - Reactions
   - Villain actions (malice cost)
   - Conditions it applies or starts with
   - Passive traits (always-on abilities)
7. **Variants** — Should this creature have a minor variant table (d10, flavor tweaks), a major variant table (d4, significant changes), or a combined variants table (d6)? Describe the variants if yes.
8. **Manner** — (Optional) A short phrase describing its behavioral disposition in combat (e.g. "Threatening Opportunism", "Pack Instinct").

Once you have the DM's answers, exit plan mode.

## Phase 2: Propose the Statblock

Build the JSON entry using exactly the schema of existing entries in `GM_notes/Beastiary/beastiary.json`. Rules:

- **Variants:** Transcribe only what the DM described — do not invent names, effects, or entries that weren't specified. If the DM gave partial variants, record them as-is and leave gaps rather than filling them in.

- Only include fields that apply. Omit `bonus_actions`, `reactions`, `villain_actions`, `conditions`, `traits`, `variants`, `minor_variants`, `major_variants`, and `manner` if they have no content.
- `ability_scores`: always include all six, each with `score` and `modifier` (modifier = floor((score-10)/2)).
- `roles` is always an array even if one element.
- `morale`: use a number if it's a save threshold, a string like `"Does not flee"` if unconditional.
- `on_hit` is always an array. Damage entries are `{ "damage": [{ "amount": "XdY+Z", "type": "type" }] }`. Non-damage effects are plain strings in the same array.
- `hit_dice` is optional — include only if provided.
- For Mob entries, include `actions_per_round` and `max_attacks_per_opponent` on relevant actions.

Show the full proposed JSON object (not wrapped in the outer `entries` array) and ask: **"Does this look right? Any changes before I append it?"**

### Schema Examples

**Basic action with damage:**
```json
{
  "name": "Shortsword",
  "type": "Melee Weapon Attack",
  "to_hit": 3,
  "on_hit": [
    { "damage": [{ "amount": "1d6+1", "type": "piercing" }] }
  ]
}
```

**Action with mixed on_hit (damage + effect):**
```json
{
  "name": "Brass Foot Kick",
  "type": "Melee Weapon Attack",
  "to_hit": 7,
  "on_hit": [
    { "damage": [{ "amount": "1d8+4", "type": "bludgeoning" }] },
    "Target is pushed 10 ft.",
    "Target is knocked prone."
  ],
  "on_miss": [
    "Murry falls prone."
  ]
}
```

**Ranged action:**
```json
{
  "name": "Hand Crossbow",
  "type": "Ranged Weapon Attack",
  "to_hit": 3,
  "range": "30/120 ft.",
  "on_hit": [
    { "damage": [{ "amount": "1d6+1", "type": "piercing" }] }
  ]
}
```

**Mob action (with actions_per_round and max_attacks_per_opponent):**
```json
{
  "name": "Shortsword",
  "type": "Melee Weapon Attack",
  "to_hit": 3,
  "max_attacks_per_opponent": 2,
  "actions_per_round": "1d6",
  "on_hit": [
    { "damage": [{ "amount": "1d6+1", "type": "piercing" }] }
  ]
}
```

**Triggered action:**
```json
{
  "name": "Stumble Through",
  "trigger": "When subject to forced movement.",
  "effect": "Murry moves half the intended distance and crashes into the nearest creature or object. That creature makes a DC 13 Dexterity saving throw or takes 1d6 bludgeoning and falls prone.",
  "save": { "dc": 13, "ability": "Dexterity" },
  "damage": [{ "amount": "1d6", "type": "bludgeoning" }]
}
```

**Reaction:**
```json
{
  "name": "I've Had Worse",
  "uses": "1/encounter",
  "trigger": "When first reduced below half HP.",
  "effect": "Murry immediately regains 10 HP."
}
```

**Villain actions:**
```json
{
  "trigger": "At the end of an opponent's turn",
  "actions": [
    {
      "name": "Action 1",
      "malice_cost": 3,
      "effect": "Description of the effect."
    },
    {
      "name": "Action 2",
      "malice_cost": 5,
      "effect": "Description with save.",
      "area": "15 ft.",
      "save": { "dc": 14, "ability": "Constitution" }
    }
  ]
}
```

**Condition:**
```json
{
  "name": "Drunk",
  "effects": [
    "All attack rolls at disadvantage.",
    "Advantage on saves against frightened and charmed."
  ]
}
```

**Passive trait:**
```json
{
  "name": "Mob Mentality",
  "effect": "While above half HP, this creature has +5 to all morale saves."
}
```

**Minor variants (d10):**
```json
{
  "roll": "1d10",
  "table": [
    { "result": 1,  "name": "None" },
    { "result": 2,  "name": "Knife Fighter", "effect": "+1 to hit with all attacks." }
  ]
}
```

**Major variants (d4):**
```json
{
  "roll": "1d4",
  "table": [
    { "result": 1, "name": "Deserter Soldier", "effect": "AC 14, +2 to hit, trained weapon use." }
  ]
}
```

**Morale — numeric vs. string:**
```json
"morale": 8
"morale": "Does not flee"
```

Do not write anything to disk until the DM confirms.

## Phase 3: Append

After confirmation, read `GM_notes/Beastiary/beastiary.json`, insert the new entry as the last element of the `entries` array (before the closing `]}`), and write the file. Preserve all existing formatting — 2-space indentation, blank lines between logical sections within entries, trailing newline.

Confirm the entry was added and show the creature's name.
