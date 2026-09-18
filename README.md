# Roguemon Move Calculator

Pick your Pokémon and up to four moves. Against every Pokémon, the page takes your
hardest-hitting move and reports:

- average best-move power
- median best-move power
- how many Pokémon take power ≥ 120
- how many Pokémon take power < 60

**Effective power** = base power × type effectiveness × 1.5 (STAB, if the move matches your
Pokémon's type) × 2 (if the move always lands a critical hit: Flower Trick, Frost Breath,
Storm Throw, Surging Strikes, Wicked Blow, Zippy Zap) × held-item boost (optional; Gen 9 values:
type items, Plates and incenses ×1.2, Life Orb ×1.3, Expert Belt ×1.2 on super-effective hits,
Muscle Band / Wise Glasses ×1.1 on physical / special moves).

Special cases handled: Freeze-Dry (super effective on Water), Flying Press (Fighting + Flying),
Thousand Arrows (hits Flying types). Not counted: abilities, items, weather, and moves
with variable or fixed damage (Low Kick, Seismic Toss, etc.). Alternate forms are listed
once per distinct typing.

## Files

| File | What it is |
|---|---|
| `index.html` | The page |
| `data.js` | Move + Pokémon data used by the page (generated) |
| `data/moves.txt`, `data/pokemon_raw.txt`, `data/items.txt` | Raw extracts from pokemondb.net (`/move/all`, `/pokedex/all`, `/item/all`) |
| `data/build_data.py` | Rebuilds `data.js` from the raw extracts: `python3 data/build_data.py` |

## Publish on GitHub Pages

1. Create an empty repo on GitHub, e.g. `roguemon-move-calculator`.
2. From this folder:
   ```sh
   git init -b main
   git add .
   git commit -m "Roguemon Move Calculator"
   git remote add origin https://github.com/<you>/roguemon-move-calculator.git
   git push -u origin main
   ```
3. On GitHub: **Settings → Pages → Build and deployment → Source: Deploy from a branch**,
   branch `main`, folder `/ (root)`. Save.
4. After a minute the site is live at `https://<you>.github.io/roguemon-move-calculator/`.

The URL remembers your loadout (e.g. `#mon=Pikachu&item=Magnet&moves=Thunderbolt|Iron Tail|...`), so
"Copy share link" gives a link to the exact setup.

## Tickets

Work items live in [`tickets/`](tickets/README.md).
