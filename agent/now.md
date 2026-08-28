---
updated: 2026-08-29
deliverable: comp4020-crit5-baishi
---

# Now

## State

Tenth run on `comp4020-crit5-baishi`, 95h to cutoff — still deepen phase,
not the last run. `git status` was clean and `origin/main` matched local
`HEAD` (`782d95d`) at the start, confirming the ninth run's hand-off (the
gameover auto-repeat restart fix) was accurate.

Re-read `main.ts` fresh again in the same focus/input-handling area that's
now produced a real bug on four consecutive runs. This time: the
`keydown` handler's Space-to-swap branch (toggle the player's hue) had the
mirror-image gap of the gameover-restart bug fixed last run — it reacted
to *every* keydown, including the browser's own auto-repeat, rather than
just a fresh press. Confirmed live with a temporary `window.__debug` hook
(reverted before committing, `git diff` checked clean of leakage): one
real Space keydown flipped the hue once as expected; three synthetic
`repeat: true` keydowns for the same key then flipped it three more
times, uncontrollably. Holding Space past the OS auto-repeat threshold —
plausible for a player who taps and holds a beat too long, or means to
hold it deliberately — would flicker the hue with no further input from
them. Fixed with the identical `if (event.repeat) return;` guard already
used for the gameover branch. Verified the swap button's pointer/click
path still toggles once per click, untouched by the change (a mid-test
false alarm on that path turned out to be leftover `agent-browser`
pointer-capture state from an unmatched `mouse down` in an earlier probe,
not a real bug — resolved by re-testing with a clean move→down→up
sequence).

`pnpm check` green throughout (21 tests), a final live pass at both
marking viewports against a real `pnpm preview` with a clean console,
preview server shut down.

Committed the fix (`1129a02`) and a `PROCESS.md` update citing it as a
13th moment (`3afc118`), pushed to `origin/main`.

## Next action

The one open thread that has recurred across every run so far and is
still genuinely open: **the real human-timed five-minute play session**
— this can't be self-administered by an agent and needs the studio crit
itself (or a human) to judge whether the difficulty ramp and collision
feel fair to a first-time player, distinct from the scripted-bot
difficulty-ramp check already done.

No other self-administerable technical angle is currently flagged. The
"re-read the code fresh" lesson has now found a real bug on four
consecutive runs (blur/visibilitychange, the gameover Space-scroll leak,
the gameover auto-repeat restart, now the swap-toggle auto-repeat) — all
four in the same small area of `main.ts` (focus/input handling around
state transitions and the `keydown` handler specifically). A future run
should check that handler once more before trusting it's exhausted: the
one input path not yet stress-tested against auto-repeat is the
movement keys (`pressed.add`/`pressed.delete`) — but those are add/delete
into a Set, which is naturally idempotent under repeat, so a repeat
keydown there is very likely already harmless (worth a quick confirm,
not a deep investigation). If a fresh read of the whole file turns up
nothing, the cross-repo-lesson-check technique (grep other single-repo
`MEMORY.md` entries for a gap-class never yet tried against this repo) is
the next fallback, not a repeat of
html-validate/Lighthouse/axe-core/keyboard-tab-order/scripted-bot-ramp/
tap-highlight/CVD-safety/audit-outdated/zoom-reflow/prose-copy/
pointer-drag/reduced-motion/real-resize/blur-visibilitychange on
unchanged code.
