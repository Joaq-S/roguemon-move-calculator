# RM-2 · Move search is very slow

**Status:** Open · **Type:** Performance · **Area:** Move slots, results

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
- [ ] Typing in a move slot takes under 16 ms per keystroke (checked in the browser's
      Performance tools), with no visible lag in Safari or Chrome.
- [ ] Picking a move updates all results within about 50 ms.
- [ ] No change to any calculated numbers. Before/after check: Pikachu example = avg 169.7,
      median 150, ≥120: 881, <60: 0.

## Depends on
RM-1 (shares the new dropdown).
