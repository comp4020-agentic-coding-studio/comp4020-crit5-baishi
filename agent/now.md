---
updated: 2026-08-28
deliverable: comp4020-crit5-baishi
---

# Now

## State

Eighth run on `comp4020-crit5-baishi`, 112h to cutoff — still deepen phase,
not the last run. `git status` was clean and `origin/main` matched local
`HEAD` (`8134ce3`) at the start, confirming the seventh run's hand-off
(the blur-vs-visibilitychange fix) was accurate.

Re-read `main.ts`/`game-logic.ts`/`index.html`/`styles.css` fresh again,
per the standing lesson to prefer that over re-running the exhausted
sensor battery. Found a real, previously-untested bug: the `keydown`
handler's `gameover` branch calls `resetGame()` and returns *before*
reaching the function's `event.preventDefault()` calls further down — so
pressing Space to restart after a loss also lets the browser's own
default page-scroll-down action for Space go through underneath the
restart.

This is invisible at both marking viewports (1920×1080, 390×844):
confirmed live that the page's total layout height never exceeds either
viewport, so there's nothing to scroll into. But the canvas is sized
`min(70vh, 32rem)` in CSS, which shrinks with the *actual* viewport
height — and a real phone's address bar being on screen effectively
reduces that below what `390×844` alone assumes. Tested a range of
shorter heights via `agent-browser set viewport 390 <h>`: the page became
genuinely scrollable at 600/500/400px tall (4–64px of real overflow),
not scrollable at 844/700. Confirmed the actual bug live with a
temporary `window.__debug` hook (same pattern used elsewhere in
`MEMORY.md` for module-scoped state): forced game-over, dispatched a
real `agent-browser press Space`, read `window.scrollY` before/after —
0 → 4 under the old code. Fixed by moving the Space-specific
`preventDefault()` ahead of the early return (the movement/hue-swap
*behaviour* still only runs during `"playing"`, only the scroll
suppression is now unconditional). Re-tested: 0 → 0 after the fix, game
still correctly transitions to `"playing"`. Reverted the debug hook
before finishing, confirmed the diff was exactly the real fix (no hook
leakage) before committing.

`pnpm check` green throughout (21 tests), a final live pass at both
marking viewports against a real `pnpm preview` with a clean console,
preview server shut down.

Committed the fix (`b3b2b60`) and a `PROCESS.md` update citing it as an
11th moment (`60317da`), pushed to `origin/main`.

## Next action

The one open thread that has recurred across every run so far and is
still genuinely open: **the real human-timed five-minute play session**
— this can't be self-administered by an agent and needs the studio crit
itself (or a human) to judge whether the difficulty ramp and collision
feel fair to a first-time player, distinct from the scripted-bot
difficulty-ramp check already done.

No other self-administerable technical angle is currently flagged. The
"re-read the code fresh, don't just re-run the exhausted battery" lesson
has now found a real bug on two consecutive runs (blur/visibilitychange,
then this scroll leak) — worth trusting as the default move over
reaching for another synthetic tool pass. If a future run exhausts a
fresh code read too, the cross-repo-lesson-check technique (grep other
single-repo `MEMORY.md` entries for a gap-class never yet tried against
this repo) is the next fallback, not a fourth repeat of
html-validate/Lighthouse/axe-core/keyboard-tab-order/scripted-bot-ramp/
tap-highlight/CVD-safety/audit-outdated/zoom-reflow/prose-copy/
pointer-drag/reduced-motion/real-resize/blur-visibilitychange on
unchanged code.
