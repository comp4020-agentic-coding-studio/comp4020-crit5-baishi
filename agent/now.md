---
updated: 2026-08-26
deliverable: comp4020-crit5-baishi
---

# Now

## State

Third run on `comp4020-crit5-baishi`, 154h to cutoff — still deepen phase,
not the last run. Started from the second run's state (nine commits, all
pushed) and worked through the exact list its own `now.md` had flagged as
untried: html-validate re-run, Lighthouse, a live keyboard tab-order walk,
and a real by-eye playtest (as opposed to the prior run's scripted bot).

1. **html-validate re-run: clean.** Same expected doctype/void-style
   non-issues as every prior repo in this pattern, nothing else fired.
2. **Lighthouse (never run on this repo before): found a real issue,
   fixed.** `best-practices` scored 0.96 for a genuine console error —
   the browser's own implicit `favicon.ico` 404, since none existed. Same
   class of finding as crit-4. Added `public/favicon.svg` (a small
   ink-dot icon in the game's own sky-blue/amber pair) and linked it from
   `index.html`; re-ran Lighthouse to confirm `best-practices` back to
   1.0 and `errors-in-console` 0 → 1, not just assumed fixed.
   `seo`/`agentic-browsing` stayed at their expected sub-1.0 scores
   (missing robots.txt/llms.txt) — not worth chasing on a tiny static
   single-page site, matching the established lesson.
3. **A second, unrelated gap found while checking the palette: fixed.**
   The colourblind-safety hue swap (a prior run's moment 4, teal/pink →
   sky blue/amber) only ever touched `main.ts`'s in-game colours —
   `styles.css`'s header nav link was still the retired pink `#f472b6`.
   Not an accessibility bug on its own (a single link colour, nothing to
   pair it against), but a real palette-consistency gap: the site's own
   chrome still carried a colour the game itself had dropped. Moved it to
   the amber already in the settled pair (8.7:1 contrast against the body
   background, confirmed by computing WCAG relative luminance directly).
   [`48e382b`](https://github.com/comp4020-agentic-coding-studio/comp4020-crit5-baishi/commit/48e382b)
4. **Live keyboard tab-order walk: clean.** Tab order is nav link → canvas
   → (wraps, nothing else focusable) — both stops carry the browser's
   default visible outline (no `outline: none` reset in `styles.css`).
   Keyboard controls (arrows/A/D to move, Space to swap) are wired on
   `window`, not gated behind canvas focus, so a stranger can start
   playing by keyboard immediately without ever tabbing to anything.
5. **Real by-eye playtest (not the scripted bot): confirms the design
   reads well, no new bug found.** Screenshotted a live session end to
   end: the two hues are clearly distinct from each other and from the
   dark canvas background at both a glance and in motion; the swap
   button's dashed outline (drawn in the colour you'd switch *to*) reads
   as visually distinct from a real obstacle; the game-over screen (bold
   text, final score, a restart icon, no instructional copy) teaches the
   restart affordance the same way the opening screen teaches the first
   move; restart-by-keypress and the score/announcer reset were confirmed
   live. No design change came out of this pass — a legitimate outcome,
   since the spec's "found by playing" requirement was already satisfied
   by the swap-button fix on the very first build run.

`pnpm check` green throughout (21 tests, unchanged). `pnpm check:evidence`
fails on exactly the expected single thing (no reflection yet). Fresh
axe-core sweep at both marking viewports: 0 violations (checked again since
the head/CSS changed). Two commits made this run (the favicon/nav-colour
fix, the `PROCESS.md` update), pushed to `origin/main` (`21c4d97`).
`PROCESS.md` now at 8 cited moments.

## Next action

Still mid-deepen, no reflection yet — correct this far from cutoff. What's
now genuinely untried, roughly in order of likely value:

- A real human-timed five-minute session judged for *feel*, not just
  survivability — this run's playtest was thorough but still solo/agent-eye;
  the brief's own bar ("a stranger can pick it up and reach an ending inside
  five minutes," judged live by the pod) is the one check that can't be
  self-administered at all. Nothing left to simulate here; note it as an
  open thread for the studio crit itself, same as crit-4's pad-count/range
  question.
- `pnpm audit`/`pnpm outdated` haven't been run on this repo yet — cheap,
  and per `MEMORY.md`'s established distinction (`audit` + in-range
  `update` is worth trying; a major-version `outdated` bump usually isn't
  this close to a template's pin) worth a look next run.
- A 200%-browser-zoom reflow check (WCAG 1.4.10, the `style.zoom` technique
  logged in `MEMORY.md`) hasn't been tried on this repo — the canvas is
  `width: 100%; height: min(70vh, 32rem)`, worth confirming it reflows
  without horizontal scroll and stays legible zoomed.
- The prose/copy pass (does the page's own text match what the interaction
  actually does — the lens that caught a real bug on assignment-1) hasn't
  been run here yet; this page has very little copy (title + meta
  description + one nav link), so it may come back "nothing to check," but
  worth a quick read.
