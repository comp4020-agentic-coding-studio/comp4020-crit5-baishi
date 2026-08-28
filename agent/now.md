---
updated: 2026-08-28
deliverable: comp4020-crit5-baishi
---

# Now

## State

Ninth run on `comp4020-crit5-baishi`, 106h to cutoff — still deepen phase,
not the last run. `git status` was clean and `origin/main` matched local
`HEAD` (`2910e20`) at the start, confirming the eighth run's hand-off (the
gameover Space-scroll fix) was accurate.

Re-read `main.ts` fresh again, per the standing "prefer a fresh code read
over re-running the exhausted battery" lesson — it's now found a real bug
on three consecutive runs. This time: the `keydown` handler's `gameover`
branch treats *every* keydown as a restart request, with no check for the
browser's own key auto-repeat (`event.repeat`). Dying usually happens
mid-dodge, with a movement key still physically held down — and a held
key keeps sending repeat `keydown` events for as long as it stays down.
That auto-repeat was silently resetting the round before the player ever
saw the game-over screen or their score, with no intentional keypress
involved at all.

Confirmed live with a temporary `window.__debug` hook (same pattern used
throughout `MEMORY.md`, reverted before committing — `git diff` checked
clean of hook leakage before staging): forced gameover with ArrowLeft
still "held" (added to the `pressed` set via a real keydown dispatch),
then dispatched a synthetic `keydown` for the same key with
`repeat: true` — state flipped straight back to `"playing"` under the old
code, announcer cleared, no player action beyond continuing to hold a key
they were already holding. After the fix (`if (event.repeat) return;`
ahead of `resetGame()`), the same repeat event no longer restarts, while
a release-and-repress of the same key, or any fresh different key, still
does. Verified all three cases live before reverting the debug hook.

`pnpm check` green throughout (21 tests), a final live pass at both
marking viewports against a real `pnpm preview` with a clean console,
preview server shut down.

Committed the fix (`f43833d`) and a `PROCESS.md` update citing it as a
12th moment (`beadbe8`), pushed to `origin/main`.

## Next action

The one open thread that has recurred across every run so far and is
still genuinely open: **the real human-timed five-minute play session**
— this can't be self-administered by an agent and needs the studio crit
itself (or a human) to judge whether the difficulty ramp and collision
feel fair to a first-time player, distinct from the scripted-bot
difficulty-ramp check already done.

No other self-administerable technical angle is currently flagged. The
"re-read the code fresh" lesson has now found a real bug on three
consecutive runs (blur/visibilitychange, the gameover Space-scroll leak,
now the gameover auto-repeat restart) — all three were in the same small
area of `main.ts` (focus/input handling around state transitions), which
suggests that area specifically rewards another close read before
trusting it's exhausted, more than the rest of the file. If a future run
reads it fresh again and finds nothing, the cross-repo-lesson-check
technique (grep other single-repo `MEMORY.md` entries for a gap-class
never yet tried against this repo) is the next fallback, not a repeat of
html-validate/Lighthouse/axe-core/keyboard-tab-order/scripted-bot-ramp/
tap-highlight/CVD-safety/audit-outdated/zoom-reflow/prose-copy/
pointer-drag/reduced-motion/real-resize/blur-visibilitychange on
unchanged code.
