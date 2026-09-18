# RM-3 · Add an optional held item that boosts move power

**Status:** Done · **Type:** Feature · **Area:** Loadout, calculation, data

## Goal
Add an optional **Held item** picker under "Your Pokémon". The item applies an extra
multiplier to qualifying moves, e.g. Silk Scarf boosts Normal-type moves.

## Scope: items to include (source: pokemondb.net/item/all)
- **Type-boosting items:** Silk Scarf, Charcoal, Mystic Water, Magnet, Miracle Seed,
  Never-Melt Ice, Black Belt, Poison Barb, Soft Sand, Sharp Beak, Twisted Spoon,
  Silver Powder, Hard Stone, Spell Tag, Dragon Fang, Black Glasses, Metal Coat, Fairy
  Feather. Also the Plates and the type incenses (Sea, Wave, Odd, Rock, Rose Incense).
  Each boosts moves of one type.
- **Possible extras** (to confirm): Life Orb (×1.3 all moves), Expert Belt (×1.2 on
  super-effective hits only), Muscle Band (×1.1 physical), Wise Glasses (×1.1 special).
- **Out of scope:** Choice Band/Specs and other items that change stats rather than move
  power; one-use Gems.

## Behavior
- Searchable picker using the same dropdown as RM-1/RM-2. Starts as "No item".
- Each move's detail line shows the item boost when it applies (e.g. "Silk Scarf ×1.2").
- Formula becomes: base power × type effectiveness × STAB × always-crit × item.
- The item is saved in the share link (`#…&item=Silk Scarf`).
- Item data is pulled from pokemondb.net into `data/items.txt`, and `build_data.py` adds it
  to `data.js`.

## Decisions (from Jason)
- Use **Gen 9 mechanics** for items, type chart and moves, including Fairy. The one exception is
  the calculator's own rule that always-crit moves count as ×2.
- So type-boosting items and Plates are **×1.2**, as are the incenses.
- Include all four extras: Life Orb, Expert Belt, Muscle Band and Wise Glasses.

## Acceptance criteria
- [x] Choosing Silk Scarf with a Normal move raises that move's effective power by the ×1.2. Moves of other types don't change.
- [x] "No item" gives the same numbers as today.
- [x] Opening a share link restores the chosen item.

## Resolution
- `data/items.txt` holds 43 items. Names and effect text come from pokemondb.net/item/all;
  the Gen 9 multipliers are added by hand, since pokemondb doesn't list them.
  `build_data.py` adds them to `data.js`.
  - 17 type-boosting items, 17 Plates and 5 incenses: ×1.2 for their type.
  - Life Orb ×1.3 on all moves; Expert Belt ×1.2 on super-effective hits only.
  - Muscle Band ×1.1 on physical moves; Wise Glasses ×1.1 on special moves.
- The "Held item" picker sits under "Your Pokémon". It uses the same searchable dropdown as the
  move slots, now shared code, and shows the full list when you click into it. Leaving it empty
  means no item.
- Each move's detail line shows the item's boost when it applies. Expert Belt shows "if
  super-effective" and is applied per target.
- The item is saved in the share link (`item=`) and cleared by "Clear all".
- Effective powers are rounded to 3 decimal places before comparing, so ×1.2 floating-point
  noise can't move a result across the 60 or 120 line.

## Verified (headless Chromium)
- Quick Attack + Silk Scarf vs Snorlax = 48 (40 × 1.2). Other move types don't change.
- Thunderbolt, no STAB: Expert Belt vs Mega Gyarados = 216 (90 × 2 × 1.2); vs Snorlax = 90
  (neutral hit, no boost). Life Orb = 117, Wise Glasses = 99, Muscle Band = 90 (special move,
  no boost).
- No item gives the same numbers as before (Pikachu example: 169.7 / 150 / 881 / 0).
- A share link with `item=Magnet` restores Magnet (Pikachu example becomes 190.3 / 162 / 881 / 0).
- Typing speed is unchanged: median 7 ms per keystroke.

## Not included
- **Fairy Feather:** not listed on pokemondb.net, our data source. Pixie Plate covers Fairy.
- **Species-only orbs:** Adamant, Lustrous and Griseous Orb, and Soul Dew. These only work on
  one Pokémon each; they could be a follow-up ticket.
- **Choice Band/Specs and Gems:** out of scope, as planned.
