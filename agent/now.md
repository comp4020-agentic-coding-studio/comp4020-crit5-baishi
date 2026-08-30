---
updated: 2026-08-30
deliverable: comp4020-crit5-baishi
---

# Now

## State

Fourteenth run on `comp4020-crit5-baishi`, 64h to cutoff — still deepen
phase, not the last run. `git status` was clean and `origin/main` matched
local `HEAD` (`12d84d0`) at the start, confirming the thirteenth run's
hand-off (the Home/End/PageUp/PageDown scroll fix) was accurate.

Followed the thirteenth run's own flagged next action: apply the
"does an already-shipped fix generalise" technique to *other* handlers,
not just the scroll-key vein (named candidates were the swap-button
hit-test, the gameover-restart-on-any-key branch, and the drag-clamp
logic). Re-reading the pointer handlers in `main.ts` found a real bug in
the drag-clamp logic: `dragging` was a single shared boolean, set true on
any `pointerdown` and cleared on any `pointerup`/`pointercancel` —
correct for a mouse (exactly one pointer ever), but wrong for touch,
where an incidental second touch (a palm edge, a bracing finger)
releasing off the canvas cleared the same flag regardless of which
pointer it belonged to.

Confirmed live with a temporary `window.__debug` hook (reverted before
finishing) and two independent synthetic `PointerEvent` identities: fired
a real drag sequence for pointer A (down, move — `playerX` tracked
correctly), then a `pointerdown`+`pointerup` for pointer B elsewhere on
the canvas, then a further `pointermove` for pointer A — and A's move was
silently dropped even though A had never released. One testing wrinkle
worth recording: `canvas.setPointerCapture()` throws `NotFoundError` for
a second synthetic pointerId while a first synthetic pointer already
holds capture (real hardware wouldn't hit this — it's an artifact of
simulating multi-touch via `dispatchEvent` rather than genuine hardware,
matching the already-logged `-p ios`/`xcrun simctl` gap), but the
underlying bug — B's `pointerup` alone clearing the shared flag — was
confirmed independently of that artifact and doesn't depend on it.

Fixed by replacing the boolean with `draggingPointerId: number | null`,
checked/set/cleared by `event.pointerId` at every one of its five call
sites (`pointerdown`, `pointermove`, `endDrag`, `releaseHeldInput`,
`gameOver`, `update`). Re-verified live post-fix: the same repro now
leaves A's drag completely unaffected by B's down/up, a chord tap on the
swap button by a second pointer mid-drag still works, and the console
stayed clean throughout at both marking viewports. `pnpm check` green
(21 tests, typecheck clean). Committed the fix (`24abb55`) and a
`PROCESS.md` update citing it as a 17th moment (`b3d0d8e`), pushed to
`origin/main`.

## Next action

The one open thread that has recurred across every run so far and is
still genuinely open: **the real human-timed five-minute play session** —
this can't be self-administered by an agent and needs the studio crit
itself (or a human) to judge whether the difficulty ramp and collision
feel fair to a first-time player.

The "does an already-shipped fix generalise" technique found something
real again, applied to a handler other than the scroll-key vein — worth
trying on the two other candidates this run didn't get to: the
gameover-restart-on-any-key branch (does `resetGame()` on `pointerdown`
during gameover have any repeat/cross-pointer edge case analogous to the
keydown `event.repeat` guard?) and the swap-button hit-test itself (is
`withinSwapButton`'s radius check ever wrong at an extreme viewport size,
given it's computed from a fixed `swapButton.radius + 12` rather than
anything viewport-relative?). If a future run tries both and finds
nothing, that's the expected state, not a reason to invent busywork.
