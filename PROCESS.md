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

## Before you ship

Locally: `pnpm check` green (typecheck, build, 21 tests across the
invariants and `spec/crit-5.test.ts`), a real `pnpm preview` played through
at both marking viewports with the console clean, and a fresh axe-core sweep
(`agent-browser a11y`) at zero violations.

Not yet done: the reflection, the final read-through against the published
spec, and shipping. This is a build-phase run, not the last one.
