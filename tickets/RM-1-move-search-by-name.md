# RM-1 · Move search should show and match move names only

**Status:** Done · **Type:** Bug / UX · **Area:** Move slots

## Problem
Typing in a move slot shows suggestions like "Normal · 120" and matches on that text, so
searching "120" or "Normal" returns moves and the move name isn't the main thing you see.

**Cause:** the move suggestions use the browser's built-in `<datalist>`. Each option has the move
name as its value and "Type · Power" as its label. Chrome shows the label prominently and
searches both.

## Desired behavior
- Suggestions list the **move name** (Absorb, Acid, …) as the main text.
- Typing matches **move names only**: "120" or "Normal" should not match on type or power.
- Type and power can still appear as small secondary detail (type chip + BP) beside the name.

## Proposed fix
Replace the `<datalist>` on the move inputs with a small custom suggestion dropdown:
- It filters by name only. Names that start with the text rank first, then names containing it.
- Each row shows the move name, then a small type chip and BP.
- Keyboard: ↑/↓ to move, Enter to pick, Esc to close. Clicking or tapping also picks.
- Accessible: it follows the ARIA combobox/listbox pattern, including `aria-activedescendant`.

## Acceptance criteria
- [x] Typing "abs" shows Absorb first; the visible text of each row is the move name.
- [x] Typing "120" or "normal" shows no suggestions based on power or type.
- [x] You can pick a move with the mouse, a tap, or the keyboard, and results update.
- [ ] Works in Chrome, Safari, Firefox and at phone width, in light and dark mode. (Only Chromium checked so far; Safari and Firefox still need a check by hand.)

## Notes
Overlaps with RM-2: the same new dropdown fixes most of the slowness.

## Resolution
- Replaced the move `<datalist>` with a custom dropdown. It matches names only, ranking
  prefix matches, then word-start matches, then other matches. Shows up to 12 results, each
  with a type chip and BP, and highlights the matched letters.
- Keyboard: ↑/↓, Enter/Tab to pick, Esc to close. Clicking a row also picks it.
- The red "not a move" state now waits until you leave the field instead of appearing while
  you type.
- Checked in headless Chromium: "abs" → Absorb; "120" and "normal" → no matches;
  "punch" → the Punch moves; keyboard and click picking both update results.
