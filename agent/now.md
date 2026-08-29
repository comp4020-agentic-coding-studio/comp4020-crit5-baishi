---
updated: 2026-08-30
deliverable: comp4020-crit5-baishi
---

# Now

## State

Thirteenth run on `comp4020-crit5-baishi`, 71h to cutoff — still deepen
phase, not the last run. `git status` was clean and `origin/main` matched
local `HEAD` (`6730ca1`) at the start, confirming the twelfth run's
hand-off (the ArrowUp/ArrowDown scroll fix) was accurate.

Followed the twelfth run's own flagged next action: re-check other
already-shipped single-key fixes for the "scoped to the key that motivated
it, not the full class" gap. Applied it to the fix that flagged it, one
level deeper — the Space→ArrowUp/ArrowDown scroll-suppression fix reasoned
about "browser scroll keys with no in-game use" as a class but only
actually covered the two arrow keys plus Space; Home, End, PageUp and
PageDown are exactly the same class and had never been checked.

Confirmed live at a real short viewport (390×500, genuine overflow) with
the canvas actually focused: each of the four keys moved `window.scrollY`
during ordinary play — End 0→25, PageDown 0→27; Home and PageUp needed
testing from a pre-scrolled position (30→4, 30→7) since scrollY was
already 0 going further up. Fixed by widening the same unconditional
`preventDefault()` check to all eight keys (Space/Spacebar, both arrows,
Home, End, PageUp, PageDown). Verified afterwards: all four no longer move
`scrollY`, ArrowRight movement and Space's hue-toggle still fire normally,
console stayed clean throughout.

Rebuilt, `pnpm check` green (21 tests). Committed the fix (`212b0b5`), a
`PROCESS.md` update citing it as a 16th moment (`ab34cbf`), pushed to
`origin/main`.

This closes the standard-browser-scroll-key class completely for this
repo — Space, both arrows, Home, End, PageUp, PageDown is the full set a
keyboard can use to scroll a page by default. A future run shouldn't
expect a fifth bug in this exact vein here.

## Next action

The one open thread that has recurred across every run so far and is
still genuinely open: **the real human-timed five-minute play session** —
this can't be self-administered by an agent and needs the studio crit
itself (or a human) to judge whether the difficulty ramp and collision
feel fair to a first-time player, distinct from the scripted-bot
difficulty-ramp check already done.

No other self-administerable technical angle is currently flagged. The
"does an already-shipped fix generalise as far as its own reasoning
implies" technique (now proven twice, once per key-class instance) is
still worth trying against *other* fixes in `main.ts`'s `keydown`/
`pointerdown` handlers — e.g. does the swap-button hit-test, the
gameover-restart-on-any-key branch, or the drag-clamp logic have a
similarly under-generalised sibling case — but the specific scroll-key
vein is now exhausted. If a future run can't find a new technical angle
either, that's the expected state, not a reason to invent busywork.
