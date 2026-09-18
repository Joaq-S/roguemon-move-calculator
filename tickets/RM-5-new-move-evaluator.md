# RM-5 · "New Move" evaluator

**Status:** Done · **Type:** Feature · **Area:** Loadout, results

## Goal
When the game offers a new move (level-up, TM, tutor), pick it in the calculator and get a clear
answer: **teach it or not**, and if yes, **which current move to forget, and why**.

## Behavior
### Input
- A new **"Evaluate a new move"** panel with one searchable move box, using the same dropdown as
  the move slots.
- It uses your current Pokémon (for STAB), held item and 4 moves.

### What it calculates
For the current loadout, and for each of the 4 possible swaps (the new move replaces slot 1, 2, 3
or 4), recalculate the four existing stats across all 1,122 Pokémon:
average, median, count at 120 or more, count under 60.

If a slot is empty, the "swap" is simply filling it, and that becomes the recommendation whenever
the new move adds anything.

### Recommendation
- A verdict card at the top: **"Teach X, forget Y"** or **"Don't teach X"**.
- A **reason**, written from the numbers. For example:
  - "Quick Attack is your best move against only 3 Pokémon, so forgetting it costs almost
    nothing."
  - "Surf becomes your best move against 214 Pokémon."
  - "Average goes 169.7 → 181.2, and the number under 60 goes from 5 to 0."
  - For "don't teach": "Every swap lowers your average. The new move is never your best option
    against more than N Pokémon."
- A comparison table: "Keep current" plus the 4 swaps, each with its 4 stats and the change
  from current. The recommended row is highlighted.
- Per option, the details behind the reason: how many Pokémon the forgotten move was uniquely
  best against (what you lose), and how many the new move becomes best against (what you gain).
- An **"Apply"** button that makes the recommended swap in the move slots.

### Edge cases
- The new move is already in your loadout: say so, with no recommendation.
- It isn't a damaging move with fixed power, e.g. a status move: say it can't be evaluated here.
- No moves in the loadout: recommend teaching it into slot 1.
- The new move is saved in the share link (`new=`).

## Decisions (from Jason)
1. Rank swaps with a **weighted mix of all four stats**. Proposed default weights, which you can
   change on the page:
   **Score = 1 × average + 0.5 × median + 0.5 × (% of Pokémon ≥120) − 2 × (% under 60).**
   In words, putting 1% of all Pokémon (about 11) under 60 costs as much as 2 points of average.
2. Say **"Don't teach" only if no swap raises the score**. Any gain, however small, means teach.

## Out of scope
Factors the calculator doesn't model: accuracy (outside RM-4's multi-hit moves), PP, recoil,
side effects, status moves, and attacking stats (Attack vs Sp. Atk).

## Acceptance criteria
- [x] Picking a new move shows a verdict, a one-paragraph reason, and a 5-row comparison table.
- [x] The recommended swap is the best option under the agreed ranking rule, checked by hand on
      at least two loadouts.
- [x] "Apply" puts the new move into the recommended slot, and all results update.
- [x] A move that makes every swap worse gets "Don't teach".
- [x] Typing in the new-move box stays fast, since 4 extra full recalculations happen only when a
      move is picked.

## Resolution
- A new **Evaluate a new move** panel sits under the stat tiles. It has a move box (the shared
  dropdown), a verdict card with a bulleted reason, an **Apply** button, and a comparison table:
  "Keep current moves" plus one row per swap, showing the change from current. The recommended
  row is highlighted.
- **Scoring weights** are under a collapsible section. They're saved in this browser only
  (localStorage, fails safely) and there's a "Reset weights" button.
- **Reasons:** what the forgotten move was best against and how many Pokémon lose damage without
  it; how many the new move becomes best against or hits harder; the before → after stats and
  score; and the runner-up option. "Don't teach" gives the least-bad swap, or says it changes
  nothing.
- **Edge cases:** already known; not a damaging fixed-power move; empty slots (only the first
  empty slot is offered).
- **Share link:** `new=` is saved and restored.
- **Refactor:** `evaluate()` now calculates a loadout's results and stats and is used by both the
  main page and the evaluator. The 4 swaps are recalculated only when the loadout, the new move
  or the weights change.

## Verified (headless Chromium, Pikachu example)
- **Surf → "Teach Surf, forget Quick Attack":** score 284.0 → 295.6. Next best is forgetting
  Iron Tail, at 285.6. Hand check of the current score: 169.7 + 0.5×150 + 0.5×78.52 − 2×0 = 284.0.
- **Tackle → "Don't teach":** replacing Quick Attack leaves every number unchanged.
- **Ice Beam with slot 4 empty → "Teach Ice Beam into empty slot 4"**, score 278.9 → 327.3.
- **Thunderbolt** → "already in your moves". **Growl** → can't be evaluated.
- **Apply** puts Surf in slot 3, and the page updates to 179.5 / 150 / 922 / 0.
- **Timing:** typing in the box has a median of 1.3 ms per keystroke; picking a move (4 swaps
  recalculated) takes 4.9 ms.
