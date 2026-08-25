---
updated: 2026-08-26
deliverable: comp4020-crit5-baishi
---

# Now

## State

First run on `comp4020-crit5-baishi`, 167h to cutoff — this is the plan/build
phase, not the last run. Arrived at a fresh template (`b4b88f0`, the initial
commit, nothing else). Fetched the brief
(`crits/05-game.json`, "A game"): a tiny browser game, one mechanic minimum,
obvious in ten seconds with **no tutorial anywhere** (not even a "click to
start" or a how-to-play modal), interesting at five minutes, can be lost, one
rule under a focused automated test, and one change that came from playing
rather than reading code.

Built **Two-Tone**: a canvas dodge game. Player is a circle, movable by
arrow keys/A-D or by dragging (mouse or touch); a fixed swap-colour circle
(also bound to space) toggles which of two colours is currently "yours".
Coloured circles fall from the top, speeding up over time. A same-colour hit
passes through; a different-colour hit ends the round (game-over overlay,
restart on any key/click/tap). This is deliberately the "two mechanics that
interact" version the brief calls the harder, better move: movement (dodge)
and colour-matching interact, since either alone lets some obstacles through
but only both together clears all of them as difficulty ramps.

The rule ("different colour ends the round, same colour is safe, no overlap
is always safe") lives as pure functions in `game-logic.ts`
(`isFatalCollision`, `circlesOverlap`), no DOM/canvas dependency, tested
directly in `spec/crit-5.test.ts` — this is the spec's "one rule under a
focused automated test."

One real bug found by playing, not by reading the code: the colour-swap
button was first drawn in the same bottom corner as the player. Screenshotting
the actual 390×844 marking viewport (not the 1920×1080 one checked first)
showed a resize's `x` clamp could put the player almost on top of it, muddling
which circle was "you." Moved the button to the top-right corner, clear of the
player's whole row — same shape as the "only checked one viewport" lesson
already in `MEMORY.md`, now with a fresh instance for this repo.

Replaced the placeholder `public/card.png` (1200×630, matching the game's
palette) — the template ships a literal "Replace this card" placeholder and
nothing in `pnpm check` catches an unreplaced one.

Verified locally: `pnpm check` green (21 tests: 14 invariants + 7 in
`spec/crit-5.test.ts`), `pnpm build` clean, played through both marking
viewports against a real `pnpm preview` (console clean at both), a fresh
axe-core sweep (`agent-browser a11y`) at zero violations, `html-validate`
clean except the expected doctype-style/void-style non-issues already logged
in `MEMORY.md`. Real keyboard tab order checked: `Home` link → `#game` canvas,
default `outline: auto` visible on both (no custom outline reset in
`styles.css`). Real mouse-drag movement and a real click-to-restart on
game-over both confirmed live, not just read from the code.

Wrote a genuine `PROCESS.md` now (not deferred to the final run) with three
cited moments, all linking real commit hashes on `origin/main`. Committed in
five separate, single-concern commits (rule+test, initial build, the
play-found fix as its own commit, the card replacement, the process writeup)
and pushed — continuity for this deliverable depends on `origin/main`, not
just the local working tree, so pushing mid-week (not only on the final run)
matches how prior crits' `MEMORY.md` entries describe this repeatedly
happening.

`pnpm check:evidence` correctly fails on exactly one thing right now: no
`reflections/crit-5.md` yet. That's expected and correct — this isn't the
final run, so no reflection was written.

## Next action

This deliverable is mid-build, not finished. A future run (deepen phase, well
before the run the prompt calls "last") should:

- Actually play a full five-minute session (not just short bursts) to check
  the difficulty ramp holds up — right now `fallSpeed`/`spawnIntervalMs` cap
  out at ~24s of elapsed time (see `game-logic.ts`), which was tuned by eye,
  not by a long playtest. This is exactly the "played it, not just read it"
  kind of check the brief itself asks for, and hasn't been done at the real
  five-minute scale yet.
- Consider whether canvas-only rendering leaves anything reachable for a
  screen-reader user beyond "game over, final score N" (the one thing
  `#announcer` currently reports) — a fully non-visual player currently has
  no way to play the game itself, only to hear how it ended. Worth deciding
  explicitly whether that's an acceptable scope limit for a canvas arcade
  game (most are) rather than leaving it unconsidered.
- The CSS-property-literacy lens from crit-4 (tap-highlight,
  forced-colors, prefers-contrast) applies less directly here since the
  game surface is a `<canvas>`, not styled DOM buttons — but the swap
  button and player are drawn shapes with no DOM equivalent, so
  `forced-colors`/high-contrast users get whatever the canvas draws,
  unaffected by OS contrast themes. Worth a deliberate look at whether the
  two hues (teal `#2dd4bf` / pink `#f472b6`) are distinguishable for common
  colour-vision deficiencies — this is a game whose entire rule is "tell
  these two colours apart," so a colourblind player may be unable to play
  it at all. Not checked yet; a real, previously-untried angle specific to
  this game's mechanic (distinct from the generic a11y sweep already run).
- Real pointer-drag and keyboard tests were both done at short bursts; a
  resize-mid-interaction check (viewport swap without reload, mid-drag) —
  the pattern already logged in `MEMORY.md` for other crits — hasn't been
  tried on this repo yet.
- No touch-action/tap-highlight check done yet for the canvas element
  itself (mobile default browser behaviours on a full-bleed touch target);
  worth the same live-device-default lens used repeatedly on crit-4.
