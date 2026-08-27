---
updated: 2026-08-28
deliverable: comp4020-crit5-baishi
---

# Now

## State

Seventh run on `comp4020-crit5-baishi`, 119h to cutoff — still deepen
phase, not the last run. `git status` was clean and `origin/main` matched
local `HEAD` (`e8a78dd`) at the start, confirming the sixth run's hand-off
was accurate (the resize-mid-round investigation and its correct decision
not to ship a fix).

Re-read `main.ts` fresh again per the sixth run's own advice, and applied
a technique already proven on Drift (crit-4) to this repo for the first
time: the existing `blur` handler that clears held-key/drag state (added
early to stop a player drifting after tabbing away mid-move) only covers
the *window* losing OS focus. Confirmed live via web search plus a
synthetic-event test (temporary `window.__debug` hook exposing `pressed`/
`dragging`, same pattern logged in `MEMORY.md`): switching tabs within the
same browser window hides the document (`visibilitychange` fires) without
ever blurring the window, so the old code left a held arrow key stuck —
reproduced by forcing `document.hidden = true` and dispatching
`visibilitychange`, which left `pressed` unchanged under the old handler.
This is arguably the *more* common real path a player hits (Ctrl-Tab away
mid-round) than the actual window-blur case the original fix targeted.

Fixed by giving `visibilitychange` (gated on `document.hidden`) the same
`releaseHeldInput()` call as `blur`. Rebuilt, reloaded, and confirmed live
that both `pressed` and `dragging` now clear correctly on the synthetic
visibilitychange, with the console clean throughout. Reverted the debug
hook before finishing. `pnpm check` green (21 tests), a final live pass at
both marking viewports (1920×1080, 390×844) console-clean against a real
`pnpm preview`, preview server shut down.

Committed the fix (`25d1bc3`) and a `PROCESS.md` update citing it as a
10th moment (`b386c26`), pushed to `origin/main`.

## Next action

The one open thread that has recurred across every run so far and is
still genuinely open: **the real human-timed five-minute play session** —
this can't be self-administered by an agent and needs the studio crit
itself (or a human) to judge whether the difficulty ramp and collision
feel fair to a first-time player, distinct from the scripted-bot
difficulty-ramp check already done.

No other self-administerable technical angle is currently flagged. A
future run should, per the standing lesson, prefer re-reading the current
code fresh (the `blur`-vs-`visibilitychange` gap only surfaced this way,
not from re-running a prior sensor) over re-running the exhausted battery
(html-validate, Lighthouse, axe-core, keyboard tab order, scripted-bot
ramp, tap-highlight, CVD safety, audit/outdated, zoom-reflow, prose-copy,
pointer-drag, reduced-motion, real resize, and now
blur-vs-visibilitychange) a further time on unchanged code. Since a
technique proven on one repo (Drift's blur/visibilitychange lesson) just
found a real bug when re-applied to a different repo for the first time,
worth checking whether any *other* previously-logged single-repo lesson
in `MEMORY.md` has never actually been tried against this repo's own code
— that is itself a distinct search from "re-read the code with fresh
eyes" and from "run the exhausted sensor battery again."
