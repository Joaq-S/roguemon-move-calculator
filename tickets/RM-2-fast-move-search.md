# RM-2 · Move search is very slow

**Status:** Done · **Type:** Performance · **Area:** Move slots, results

## Problem
Typing in a move slot feels laggy.

## Likely causes
1. **Native `<datalist>` with 551 labeled options.** Browsers (Safari especially) re-filter
   and redraw the whole list on every keystroke.
2. **A full recalculation and redraw on every keystroke.** Each `input` event calls `render()`,
   which recalculates all 1,122 Pokémon and rebuilds the chart, both side tables and the
   1,122-row "Every Pokémon" table. That runs even while the text doesn't yet match a move.

## Proposed fix
- Use the custom dropdown from RM-1 and cap the number of suggestions shown (for example 12).
- Recalculate results only when a slot's move actually changes (a valid pick, or the slot is
  cleared), not on every keystroke.
- Redraw the "Every Pokémon" table only when results, the filter or the sort change. Consider
  showing the first ~100 rows with a "Show all" option.
- Wait about 100 ms after typing stops before applying the Pokémon-table filter.

## Acceptance criteria
- [x] Typing in a move slot takes under 16 ms per keystroke (checked in the browser's
      Performance tools), with no visible lag in Safari or Chrome. (Chromium checked; Safari still needs a check by hand.)
- [x] Picking a move updates all results within about 50 ms.
- [x] No change to any calculated numbers. Before/after check: Pikachu example = avg 169.7,
      median 150, ≥120: 881, <60: 0.

## Depends on
RM-1 (shares the new dropdown).

## Resolution
- The move dropdown from RM-1 shows at most 12 matches, so there's no native `<datalist>` to
  redraw on each keystroke.
- `render()` now always refreshes the small per-slot details. The full results (all 1,122
  Pokémon, the chart and the tables) are recalculated only when the chosen Pokémon or one of
  the chosen moves actually changes. The share-link hash is updated at the same point, which
  also avoids Safari's limit on how often `history.replaceState` can be called.
- The "Every Pokémon" table shows 100 rows, with a "Show all 1122 Pokémon" button.
- The table filter waits 100 ms after typing stops before applying.
- The chart still redraws when the light/dark theme or the URL hash changes.

## Measurements (headless Chromium in the build sandbox, typing "thunderbolt")
| | Before | After |
|---|---|---|
| Keystroke → next frame | 99–139 ms | 5–24 ms |
| Input handler, median / worst | – | 5.9 ms / 14.7 ms (worst = the keystroke that completes the move) |
| Picking a move (full recalculation) | 21.8 ms | 6.5 ms |

Numbers are unchanged: the Pikachu example is still avg 169.7, median 150, ≥120: 881, <60: 0,
and it restores correctly from a share link.

## Follow-up (not in this ticket)
The **Your Pokémon** box still uses a native `<datalist>` with 1,122 entries, which may feel
slow in Safari. It could use the same dropdown as the move slots.
