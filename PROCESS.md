# Process overview

## What I built

**Two-Tone**, a falling-circle dodge game with one rule: a circle the same
colour as you passes straight through, a circle the other colour ends the
round. You start with only a colour and a position; the game hands you an
easy dodge first, then keeps sending obstacles until dodging alone stops
working and matching colour is the only way through. Difficulty is time,
not text --- it never tells you the rule, it just keeps you alive until you
find it.

## The moments that mattered

1. **Choosing the mechanic to fit the no-tutorial constraint.** The brief
   rules out any how-to-play text, so the mechanic itself had to teach the
   rule by consequence rather than by instruction. A pure left/right dodge
   needs no explaining but has no depth; a colour-match-or-die rule has
   depth but risks being illegible without a caption. The fix was pacing,
   not text: the first obstacle always matches the player's colour by
   spawn odds being 50/50 and the opening obstacles being sparse, so an
   early death is either from not moving at all or from a same-colour hit
   passing through unremarked --- the *first* wrong-colour hit is the
   moment the rule teaches itself, and it always arrives inside the first
   few seconds, well within the "obvious in ten seconds" bar.
   [`68b5417`](https://github.com/comp4020-agentic-coding-studio/comp4020-crit5-baishi/commit/68b5417)
2. **Keeping the rule itself testable independent of the canvas.** Collision
   and colour-matching live in `game-logic.ts` as pure functions
   (`isFatalCollision`, `circlesOverlap`) with no DOM or canvas dependency,
   so `spec/crit-5.test.ts` asserts the one rule the spec asks for directly:
   same-hue overlap is safe, different-hue overlap ends the round, and no
   overlap is always safe regardless of colour. `main.ts` only wires that
   logic to rendering and input.
   [`2b7379d`](https://github.com/comp4020-agentic-coding-studio/comp4020-crit5-baishi/commit/2b7379d)
3. **The swap-button placement bug, found by playing, not by reading code.**
   The colour-swap control was first drawn in the same bottom corner as the
   player, sized and positioned relative to the canvas. Reading the code
   gave no reason to doubt it. Screenshotting the actual 390×844 marking
   viewport did: a resize clamps the player's `x` into the new width, and on
   the narrower canvas that clamp pushed the player almost on top of the
   swap button, so the two circles you're meant to tell apart (`you`, `the
   button`) sat side by side. Moved the swap button to the top-right, clear
   of the player's whole row, so no resize can ever put them in the same
   spot. Re-verified at both marking viewports afterwards.
   [`6778aa5`](https://github.com/comp4020-agentic-coding-studio/comp4020-crit5-baishi/commit/6778aa5)

4. **The two hues weren't actually colourblind-safe, and I checked rather
   than assumed.** The entire mechanic is "tell these two colours apart," so
   I ran the launch pair (teal `#2dd4bf`, pink `#f472b6`) through the
   Machado (2009) colour-vision-deficiency simulation matrices rather than
   eyeballing them. Under deuteranopia the two collapse to near-identical
   greys — Euclidean RGB distance ~27, against ~222 for typical vision — so
   a deuteranopic player would have had no way to play at all, not just a
   harder time. Replaced them with sky blue `#38bdf8` / amber `#f59e0b`,
   which stays well separated under protanopia, deuteranopia and
   tritanopia alike and contrasts near-equally against the canvas
   background, so neither colour reads as fainter than the other.
   [`9cdfc89`](https://github.com/comp4020-agentic-coding-studio/comp4020-crit5-baishi/commit/9cdfc89)
5. **A scripted playtest, not just arithmetic, to check the difficulty
   ramp.** `fallSpeed`/`spawnIntervalMs` both cap out by ~33 seconds of
   elapsed time, meaning the round spends most of a five-minute session at
   flat maximum difficulty. Rather than treat that as a design smell from
   the formulas alone, I drove a real reactive bot (matching colour to the
   nearest oncoming obstacle, dodging when two threats conflict) through
   the live build via `agent-browser eval`, polling actual game state every
   55ms. It survived past the five-minute mark without ever hitting an
   unfair, un-survivable obstacle pattern — confirming the post-ramp
   difficulty is a sustained challenge rather than a wall, which the
   formulas alone couldn't show.
6. **A default browser behaviour, not a bug in this code, still needed a
   fix.** The canvas had no `-webkit-tap-highlight-color` override, so
   Android/WebKit paint their default translucent grey rectangle over the
   whole play area on every tap or drag start — nothing in this game's own
   logic causes it, but a full-bleed touch surface makes it more visible
   than most. Set it to `transparent`, confirmed via
   `getComputedStyle` before and after.
   [`8b9e859`](https://github.com/comp4020-agentic-coding-studio/comp4020-crit5-baishi/commit/8b9e859)
7. **Scoped, not skipped, the screen-reader question.** A fully blind
   player can hear how the round ended (`#announcer` reports the final
   score) but has no way to play the falling-circle mechanic itself, since
   there's no non-visual channel for obstacle position or colour. For a
   canvas arcade game this is a deliberate, named scope limit rather than
   something silently left unconsidered — matching most action games of
   this shape.

## Before you ship

Locally: `pnpm check` green (typecheck, build, 21 tests across the
invariants and `spec/crit-5.test.ts`), a real `pnpm preview` played through
at both marking viewports with the console clean, and a fresh axe-core sweep
(`agent-browser a11y`) at zero violations.

Not yet done: the reflection, the final read-through against the published
spec, and shipping. This is a build-phase run, not the last one.
