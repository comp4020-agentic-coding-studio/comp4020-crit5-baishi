---
updated: 2026-08-31
deliverable: comp4020-crit5-baishi
---

# Now

## State

Seventeenth run on `comp4020-crit5-baishi`, 40h to cutoff — still deepen
phase, not the last run. `git status` was clean and `origin/main` matched
local `HEAD` (`210bc7a`) at the start, confirming the sixteenth run's
hand-off (no new bug in two straight runs) was accurate.

Re-read `main.ts`/`game-logic.ts`/`index.html`/`styles.css` fresh and
found one genuinely new angle: the CSS-property-literacy lens already
used on crit-4 (`-webkit-tap-highlight-color`, `touch-action` scope,
`forced-colors`) had a fourth, still-untried instance on this repo.
`touch-action: none` on `#game` only suppresses pan/zoom gestures — it
does not touch iOS Safari's separate long-press callout (context
menu/copy) and text-selection magnifier, confirmed via web search of MDN
and current sources. That matters specifically here because the drag
mechanic *is* a sustained touch-hold on this exact element, so an
uncontrolled callout is a real risk of interrupting a drag mid-game on
iOS, not a cosmetic nicety. Fixed with `-webkit-touch-callout: none` plus
`-webkit-user-select`/`user-select: none`, the documented pairing.
Verified scoped to `#game` only (grep) and `user-select: none` actually
applied via `getComputedStyle` against a real `pnpm preview`; the callout
itself stays unverifiable in this sandbox (no real iOS host — the same
`xcrun simctl` gap logged repeatedly elsewhere), so this is a pre-emptive,
documentation-grounded fix, same epistemic status as the tap-highlight and
forced-colors fixes on crit-4. `pnpm check` green (21 tests) both before
and after. Fixed and pushed (`e9b35f8`/`52cb922`), `PROCESS.md` now at 18
cited moments.

## Next action

The human-timed five-minute play session remains the sole standing open
thread — still can't be self-administered, needs the studio crit itself.

No new self-administered angle is currently flagged, but this run shows
the CSS-property-literacy lens (checking platform/browser defaults the
stylesheet hasn't overridden, not the app's own code) can still turn up
something real even after 16 runs of code-reading and sensor-battery
checks had gone dry. A future run might try the remaining crit-4 lesson
not yet cross-checked here in depth: `prefers-contrast`/high-contrast
variants beyond `forced-colors` (already confirmed not applicable since
this game's interactive surface is canvas pixels, not a DOM
button/border-based control) — otherwise treat "nothing new found" as the
expected steady state, not a sign something's being missed.
