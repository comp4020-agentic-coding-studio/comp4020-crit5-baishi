---
updated: 2026-08-29
deliverable: comp4020-crit5-baishi
---

# Now

## State

Twelfth run on `comp4020-crit5-baishi`, 82h to cutoff — still deepen
phase, not the last run. `git status` was clean and `origin/main` matched
local `HEAD` (`a2f7e25`) at the start, confirming the eleventh run's
hand-off (the mid-drag game-over `dragging` fix) was accurate.

Re-ran the cheap periodic checks first: `pnpm audit` still clean, `pnpm
outdated` still the same four major-only entries (correctly left alone),
`pnpm check` green (21 tests). Then did a fresh close read of `main.ts`
looking for the sixth bug in a row a fresh read might turn up — and found
one, but not a brand-new mechanism: a generalisation gap in a fix already
shipped two runs ago.

The eighth run's fix (`b3b2b60`) suppressed Space's default page-scroll
during a gameover restart, reasoning specifically about Space because
it's the browser's page-scroll-down key. But ArrowUp/ArrowDown have zero
in-game effect anywhere in `main.ts` — the game only ever reads
ArrowLeft/ArrowRight — so nothing had ever called `preventDefault()` on
them, in *any* state, not just gameover. Confirmed live: at a real short
viewport (390×500, genuine overflow — `scrollHeight` 534 vs `innerHeight`
500), pressing ArrowDown during ordinary play (no collision, no restart)
scrolled the page 0→29px. This is the same bug *class* as the Space fix,
just never generalised past the one key that motivated it.

Fixed by widening the existing unconditional preventDefault check (the
one already covering Space) to include ArrowUp and ArrowDown too.
Verified live afterwards: ArrowDown/ArrowUp no longer move `scrollY` at
the same short viewport; a real ArrowLeft press still moves the player
(confirmed via before/after screenshots, which incidentally also
reconfirmed restart-on-any-key still works, since the test round had
already ended by the time of the check); Space still suppresses scroll
and the console stayed clean throughout. Rebuilt, `pnpm check` green (21
tests), both marking viewports (1920×1080, 390×844) screenshotted against
a real `pnpm preview` with a clean console, preview server shut down
after.

Committed the fix (`79b43cc`), a `PROCESS.md` update citing it as a 15th
moment (`4b7577d`), pushed to `origin/main`.

## Next action

The one open thread that has recurred across every run so far and is
still genuinely open: **the real human-timed five-minute play session**
— this can't be self-administered by an agent and needs the studio crit
itself (or a human) to judge whether the difficulty ramp and collision
feel fair to a first-time player, distinct from the scripted-bot
difficulty-ramp check already done.

No other self-administerable technical angle is currently flagged, but
this run's finding suggests one worth trying next: re-read every other
already-shipped single-key fix in this handler (the auto-repeat guards on
gameover-restart and Space-toggle) for the same "scoped to the key that
motivated it, not generalised to the full class" gap-shape found here —
e.g. does anything else in `main.ts` special-case one key/gesture where a
sibling key/gesture with the same underlying property was left unchecked.
This one wasn't found by re-deriving a brief clause or running a sensor;
it was found by re-reading an *already-shipped fix*'s own reasoning and
asking whether it generalised as far as it should have — a different
angle from both fresh-code-reads and clause-by-clause brief derivation,
worth naming as its own technique for future runs on this file
specifically, since three techniques (fresh read, brief-clause
re-derivation, fix-generalisation-check) have now each found a real bug
here at least once.
