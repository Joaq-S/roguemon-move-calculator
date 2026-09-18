# RM-3 · Add an optional held item that boosts move power

**Status:** Open · **Type:** Feature · **Area:** Loadout, calculation, data

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

## Open questions (need answers before starting)
1. **Multiplier by generation:** type-boosting items are ×1.2 from Gen 4 onward but ×1.1 in
   Gen 3. Roguemon runs on FireRed/LeafGreen. Should we use ×1.1 (Gen 3) or ×1.2 (current)?
2. **Which extras** from the "Possible extras" list, if any, should be included?

## Acceptance criteria
- [ ] Choosing Silk Scarf with a Normal move raises that move's effective power by the agreed
      multiplier. Moves of other types don't change.
- [ ] "No item" gives the same numbers as today.
- [ ] Opening a share link restores the chosen item.
