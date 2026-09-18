# RM-4 · Count every hit of multi-hit moves

**Status:** Done · **Type:** Bug / Calculation · **Area:** Calculation, data

## Problem
Moves that hit more than once are counted as a single hit. Tachyon Cutter (50 BP, always 2
hits) shows 50 instead of 100. Arm Thrust (15 BP, 2–5 hits) shows 15.

(These moves hit several times *within one turn*. The ticket says "hits", not "turns", to keep
them separate from true multi-turn moves like Rollout; see Out of scope.)

## Desired behavior
Effective power = base power × **hits** × type effectiveness × STAB × always-crit × item.

### Fixed hit count: multiply by the guaranteed number of hits
| Hits | Moves |
|---|---|
| 2 | Bonemerang, Double Hit, Double Iron Bash, Double Kick, Dragon Darts, Dual Chop, Dual Wingbeat, Gear Grind, Tachyon Cutter, Twin Beam, Twineedle |
| 3 | Surging Strikes (also always crits: 25 × 3 × 2 = 150), Triple Dive |

### Escalating power, fixed 3 hits: sum the hits
| Move | Hits | Total |
|---|---|---|
| Triple Kick | 10 + 20 + 30 | **60** |
| Triple Axel | 20 + 40 + 60 | **120** |

### Variable hit count: use the average number of hits
2–5 hit moves: Arm Thrust, Barrage, Bone Rush, Bullet Seed, Comet Punch, Double Slap, Fury
Attack, Fury Swipes, Icicle Spear, Pin Missile, Rock Blast, Scale Shot, Spike Cannon, Tail Slap,
Water Shuriken.

- Gen 9 odds are 2 hits 35%, 3 hits 35%, 4 hits 15%, 5 hits 15%, which averages **3.1 hits**.
  Example: Arm Thrust = 15 × 3.1 = 46.5.

Population Bomb (20 BP, 1–10 hits): see the open questions.

### Source
The move list is from pokemondb.net/move/all effect text ("Hits 2-5 times", "Hits twice",
"Guaranteed to hit twice", …). Surging Strikes and Triple Axel don't mention hit counts in
pokemondb's short text, so they're added from the Gen 9 move rules.

## Implementation notes
- Add a hits column to `data/moves.txt`: a fixed number, `avg25` for 2–5 hits, or a per-hit
  list for Triple Kick/Axel. `build_data.py` passes it through to `data.js`.
- Show the hits in each move's detail line, e.g. "× 3.1 hits (2–5)" or "× 2 hits".
- In the table, show the move name with its hits factor where it applies.

## Decisions (from Jason)
1. 2–5 hit moves use **3.1 hits** (Gen 9 odds).
2. Population Bomb uses **expected hits**: each hit rolls 90% accuracy and the move stops at the
   first miss, giving Σ 0.9^k for k = 1..10 = 5.862 hits → **117.2**.
3. Triple Kick and Triple Axel use **expected damage** the same way: 10×0.9 + 20×0.81 + 30×0.729
   = **47.07**, and 20×0.9 + 40×0.81 + 60×0.729 = **94.14**. This replaces the 60 / 120 in the
   table above.
   Note: these three moves are the only place the calculator counts accuracy. It's intentional,
   because the hit count itself depends on accuracy.

## Out of scope (unless you want them)
- **True multi-turn moves** that build power over several turns: Rollout, Ice Ball, Fury
  Cutter, Echoed Voice. These stay counted as a single use.
- **Abilities and items that change hit counts:** Skill Link, Loaded Dice, Parental Bond.
- **Beat Up:** its power depends on your party, so it's already excluded.

## Acceptance criteria
- [x] Tachyon Cutter shows 100 and Surging Strikes shows 150 against a neutral target (no STAB, no item).
- [x] Arm Thrust shows 46.5 against a neutral target.
- [x] Triple Kick shows 47.1 and Triple Axel 94.1 against a neutral target (expected damage, per decision 3).
- [x] Population Bomb shows 117.2 against a neutral target.
- [x] Single-hit moves are unchanged; the Pikachu example still reads 169.7 / 150 / 881 / 0.

## Resolution
- New `data/multihit.txt` lists 31 moves, each with a power factor and a label, and the math is
  written in comments. `build_data.py` merges it into `data.js` and stops with an error if a
  name doesn't match a move.
- Formula: base power × hits × type effectiveness × STAB × always-crit × item. The crit and item
  boosts apply to every hit.
- The move details show the factor and label, e.g. "×3.1 · avg 3.1 hits (2–5)". Move
  suggestions show "15 BP ×3.1". The formula note on the page now mentions hits.
- Checked in headless Chromium against neutral targets (Snorlax/Pikachu, no STAB, no item):
  Tachyon Cutter 100, Surging Strikes 150, Arm Thrust 46.5, Triple Kick 47.1, Triple Axel 94.1,
  Population Bomb 117.2, Thunderbolt 90 (unchanged).
