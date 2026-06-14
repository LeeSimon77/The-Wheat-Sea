# Plan: Elevation Layer + River Redraw

## Context
Rivers in map_v5.json can cross mountain ranges because the old tracer allowed 1-cell uphill steps. The fix requires a proper elevation map so rivers can only flow strictly downhill. We also want swamps (where rivers pool in flat terrain / endorheic basins) and deltas (at river mouths where rivers meet the coast).

## Approach Overview
1. Derive an elevation value (0–14) for every cell by combining base + terrain layers.
2. Add it as a new "elevation" layer in the JSON — visible in the editor, toggleable like any other layer.
3. Clear the existing rivers layer and retrace every river with strict downhill-only logic.
4. Paint swamps on the terrain layer where rivers pool or slow, and at deltas.
5. Save output as **map_v6.json**.

---

## Step 1 — Elevation Mapping

Combine base and terrain IDs into a single integer 0–14:

| Base (id) | Terrain (id) | Elevation |
|-----------|--------------|-----------|
| void (0) | any | 0 |
| deep_water (1) | any | 1 |
| water (2) | any | 2 |
| shallows (3) | any | 3 |
| coast (4) | any | 4 |
| land (5) | none/plains/forest/desert (0,1,5,6) | 5 |
| land (5) | tundra (7) | 6 |
| land (5) | swamp (4) | 4 |
| land (5) | hills (2) | 8 |
| land (5) | mountain (3) | 10 |
| land (5) | peak (9) | 12 |
| land (5) | volcanic (8) | 12 |
| highlands (6) | none/plains/forest/desert | 7 |
| highlands (6) | tundra | 8 |
| highlands (6) | hills | 10 |
| highlands (6) | mountain | 12 |
| highlands (6) | peak / volcanic | 14 |
| ice (7) | any | 6 |

Formula: `elev = BASE_ELEV[base_id] + TERRAIN_BONUS[terrain_id]`, clamped 0–14.

---

## Step 2 — Elevation Palette (config.py)

Add `ELEVATION_PALETTE` (15 entries, id 0–14) with a blue→green→yellow→gray→white gradient, and add `"elevation"` to `DEFAULT_LAYERS` (inserted after `"base"`).

Elevation is NOT land-constrained (covers all cells including ocean).

---

## Step 3 — BFS dist-to-water

BFS from all cells with elevation ≤ 2 (water/deep_water) outward through all other cells. `dtw[idx]` = shortest path distance to nearest water cell. Used as tiebreaker when multiple neighbors share the same elevation.

---

## Step 4 — River Sources

Find cells where:
- Elevation ≥ 9 (mountain or above)
- At least one 4-neighbour has strictly lower elevation (edge of mountain zone)

Space sources ≥ 18 cells apart (furthest-first selection). Target ~55 sources.

---

## Step 5 — Strict Downhill River Tracing

```python
for _ in range(MAX_STEPS=500):
    candidates = []
    for n in nb4(curr):
        if elev[n] > elev[curr]: continue   # HARD: no uphill
        drop      = (elev[curr] - elev[n]) * 10
        dtw_gain  = (dtw[curr]  - dtw[n])  * 5   # bias toward water
        flat_noise = gauss(0, 1.0 if drop == 0 else 0.3)
        candidates.append((drop + dtw_gain + flat_noise, n))
    if not candidates: break   # stuck → endorheic
    move to best candidate
    if elev[next] <= 2: mark ocean-terminus; break
```

Key invariant: **`elev[n] > elev[curr]` is a hard skip** — rivers never climb. Mountain ranges become impassable walls; rivers flow only through valleys and passes that have monotonically decreasing elevation paths.

Accept river if:
- Reaches ocean AND length ≥ 15 cells, OR
- Inland (endorheic) AND length ≥ 30 cells

---

## Step 6 — Swamps

After all rivers are placed, for each accepted river path:

1. **Flat lowland stretches** (elevation 4–5): any run of ≥ 5 consecutive flat river cells → mark a radius-1 blob of surrounding land cells as swamp (terrain id 4). Captures river floodplains.

2. **Endorheic basin endpoints**: the last cell of each inland river → mark a radius-3 blob as swamp. Represents inland drainage lakes/marshes.

Only paint swamp on land-base cells (base id ∈ {4, 5, 6}) that currently have terrain id in {0, 1, 5} (none/plains/forest — don't overwrite hills/mountain/desert).

---

## Step 7 — Deltas

For ocean-terminus rivers: the last 2–3 river cells before reaching water are the "mouth". Mark coast/shallows cells within radius 2 of the mouth as swamp on the terrain layer. Gives an estuarine/delta appearance at river mouths.

---

## Files to Modify

| File | Change |
|------|--------|
| `map_editor/config.py` | Add `ELEVATION_PALETTE` (15 entries), add `"elevation"` key in `DEFAULT_LAYERS` after `"base"` |
| `GM_notes/map_notes/map_v5.json` | **Read only** — source data |

## New File

| File | Purpose |
|------|---------|
| `GM_notes/map_notes/gen_rivers_v2.py` | Standalone script: load v5 → compute elevation → retrace rivers → paint swamps/deltas → save map_v6.json |

The script is self-contained (no editor imports needed) — just standard library + numpy.

---

## Output

`map_v6.json` with:
- 6 layers: base, **elevation**, terrain, political, places_of_interest, rivers
- Elevation layer: 15-value derived heightmap, toggleable in editor
- Rivers layer: redrawn with strict downhill, no mountain crossings
- Terrain layer: swamp cells added at basins, river floodplains, and deltas

---

## Verification

1. Open map_v6.json in the map editor
2. Toggle elevation layer on → should see a coherent heightmap (ocean dark, peaks white)
3. Toggle rivers on over elevation → all rivers should visually descend from high to low elevation; no river should cross a continuous high-elevation ridge
4. Toggle terrain → swamp cells should appear at river mouths and inland endpoints
