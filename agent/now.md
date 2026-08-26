---
updated: 2026-08-26
deliverable: comp4020-crit5-baishi
---

# Now

## State

Second run on `comp4020-crit5-baishi`, 160h to cutoff — still deepen phase,
not the last run. Started from the first build run's state (five commits,
already pushed) and worked through every item its own `now.md` had flagged
as untried. Found and fixed two real issues, ran one scripted playtest that
came back clean, and made one explicit scope decision:

1. **Colourblind accessibility (real bug, fixed).** The launch hue pair
   (teal `#2dd4bf`, pink `#f472b6`) was never checked against colour-vision
   deficiency, despite the entire game rule being "tell these two colours
   apart." Ran both through the Machado (2009) CVD simulation matrices
   (verified against the `colour-science` library's published dataset, not
   guessed) — under deuteranopia they collapse to near-identical greys
   (RGB distance ~27 vs ~222 for typical vision), meaning a deuteranopic
   player couldn't have played this game at all. Replaced with sky blue
   `#38bdf8` / amber `#f59e0b`: normal/protanopia/deuteranopia/tritanopia
   distances all 239+, and both contrast near-equally (~7.9) against the
   canvas background `#171b2e`. Confirmed visually with real screenshots
   at both marking viewports, not just the numbers.
   [`9cdfc89`](https://github.com/comp4020-agentic-coding-studio/comp4020-crit5-baishi/commit/9cdfc89)
   No CSS-`filter`/CDP vision-deficiency emulation is available in this
   sandbox (`agent-browser` doesn't expose Chrome's
   `Emulation.setEmulatedVisionDeficiency`) — the matrix-math technique
   above is the workaround: compute the simulated RGB values directly
   rather than trying to render/screenshot the simulation. Worth reusing on
   any future crit whose mechanic hinges on distinguishing hues.
2. **Difficulty-ramp playtest (real play, came back clean).**
   `fallSpeed`/`spawnIntervalMs` (`game-logic.ts`) both cap out by ~33s
   elapsed, so a 5-minute round spends most of its length at flat maximum
   difficulty. Rather than treat that as a smell from the arithmetic alone,
   added a temporary debug hook to `main.ts` (`window.__debug`, exposing
   live state — obstacles, player, elapsed time — never committed, reverted
   before the final `pnpm check`), then drove a real reactive bot through
   the live build via repeated `agent-browser eval` (swap colour to match
   the nearest oncoming threat; dodge only when two threats conflict,
   polling every 55ms). It survived past the five-minute mark (312s,
   score 4821) without ever hitting an unfair/unsurvivable pattern —
   confirms the post-ramp difficulty is a sustained challenge, not a wall.
   A synthetic bot's reflexes aren't a human's, so this is "checked,
   plausible, not retuned," not proof a human finds it exactly as easy.
3. **Tap-highlight on the canvas (real bug, fixed).** Same class of finding
   as crit-4's `.pad`: `#game` had no `-webkit-tap-highlight-color`
   override, so Android/WebKit's default translucent-grey tap flash would
   cover the *entire* play area on every tap/drag-start (worse than a small
   button, since this is a full-bleed canvas). Confirmed via
   `getComputedStyle` before (`rgba(0,0,0,0.18)`) and after (`rgba(0,0,0,0)`).
   [`8b9e859`](https://github.com/comp4020-agentic-coding-studio/comp4020-crit5-baishi/commit/8b9e859)
   `touch-action: none` was already correctly scoped to `#game` only (not
   `body`), so no fix needed there — matches the crit-4 lesson about
   scoping that property to the actual interactive surface.
4. **Forced-colors / screen-reader: checked, no fix needed / scope decided.**
   Canvas *drawing content* (arcs filled via `ctx.fill()`) is immune to
   `forced-colors: active` — unlike crit-4's DOM `.pad` buttons, there's no
   `background`/`box-shadow` for Windows High Contrast to strip, so the
   crit-4 CSS-literacy lens doesn't apply here. Screen-reader play: a blind
   player can hear the final score (`#announcer`) but has no channel to
   play the mechanic itself — recorded in `PROCESS.md` as a deliberate,
   named scope limit for a canvas arcade game, not left unconsidered.
   Resize-mid-interaction (viewport swap without reload, mid-play) was also
   tried live: score/state persisted, canvas and swap-button repositioned
   correctly, console stayed clean.

All four items `now.md` had flagged after the first build run are now
closed. `PROCESS.md` extended to 7 cited moments (was 3), all resolving.
`pnpm check` green (21 tests, unchanged), `pnpm check:evidence` fails on
exactly the expected single thing (no reflection yet — correct, not the
final run). Fresh axe-core sweep at both marking viewports: 0 violations.
Real drag + swap + resize-mid-play + restart cycle confirmed live with a
clean console throughout. Also caught, while checking, that `public/card.png`
(the link-preview card) still showed the old teal/pink palette after the
hue swap — regenerated it at the same 1200×630 with the new sky/amber pair
via `agent-browser` rendering a matching layout, rather than leaving the
one surface an in-game fix can't reach on the unsafe palette. Four commits
made this run (hue fix, tap-highlight fix, PROCESS.md, card.png), all
pushed to `origin/main` (`5d63433`).

## Next action

This deliverable is still mid-deepen, not finished — no reflection yet, and
that's correct this far from cutoff. Genuinely untried angles for a future
run, roughly in order of likely value:

- A full HTML-validate pass (`pnpm dlx html-validate dist/*.html`) hasn't
  been re-run since the very first build — cheap, and per `MEMORY.md` worth
  re-checking per repo state, not assumed still clean from the first run.
- Lighthouse (best-practices/performance/CWV) hasn't been run on this repo
  at all yet — on crit-4 it caught a real favicon-console-error miss that
  every other sensor missed; worth trying here too, including checking
  `public/card.png`/favicon presence.
- A real keyboard tab-order walk (`Tab` + reading `document.activeElement`)
  hasn't been done on this specific repo yet, only inferred as "probably
  fine, default outline" — worth actually walking it per the established
  technique.
- The five-minute playtest above used a scripted bot; a real (if brief)
  human-timed play session, watching for anything that reads as
  "unfair"/"illegible" in a way a bot can't judge (a same-colour hit that
  *looks* like it should have been fatal, or vice versa, due to overlap
  math vs. visual overlap), hasn't been done. This is the "played it, not
  read it" check the brief cares most about and is worth doing directly by
  eye at least once.
