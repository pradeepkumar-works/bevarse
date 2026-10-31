# Bevarse: project instructions

Adapted from pstack (https://github.com/cursor/plugins/tree/main/pstack). Cursor-only parts (Bugbot, Grok routing, Cursor automations, cursor-team-kit skills) are left out.

## Product context

We are building Bevarse, a music app that teaches people to play songs on a keyboard. The stack is Expo (React Native) with Expo Router and TypeScript. Web ships first, then iOS, then Android. Every feature must work on web before native work starts, and nothing may be added that blocks the later native builds.

## Architecture

The design lives in the doc "Keyboard song coach: architecture design" (https://claude.ai/artifact/CyS2K7TSSHdoPhmNSTziXS). In short: pure TypeScript in `src/core` (song model, lesson reducer, scorer), platform code only in `src/adapters` (audio, midiIn, renderer, storage, spotify), and every song source parsed once into the `Song` shape at the import boundary. Spotify supplies song identity (title, art, ISRC), never notes or audio. Change the doc before you change one of these boundaries.

## How we use pstack

- Work in poteto-mode style. Use the Prototype playbook for a decision that running something can settle, and the Feature playbook for real builds.
- Delegate code-writing to `pstack:poteto-agent`. Run `pstack:comment-sicko` over the diff before review.
- Model choices per role live in `.claude/rules/pstack-models.md`. Change them with `/pstack:setup-pstack`.

## How to start any task

- Open your first reply by restating the goal, the problem it solves for the learner, and the smallest change you expect to meet it. Do not wait for confirmation. If the evidence shows the asked-for change is wrong, say so and stop.
- Before writing code, name the data shape. Prefer one typed structure over scattered conditionals.
- If a "which approach?" question could be settled by running something (layout, timing, latency, feel), build a quick throwaway prototype and let the result decide. Only ask about real product or taste calls.

## Domain rules

- A song is data, not code. Model it as a typed list of note events (pitch as MIDI number, start and duration in beats, hand, finger hint) plus tempo and time signature. Parse imported files (MIDI, MusicXML) once, at the boundary, into this shape.
- A lesson is a state machine (for example idle, count-in, playing, paused, finished). Use a reducer or explicit states, not boolean flags.
- Scoring and hit detection are pure functions of (expected notes, played notes, timing window). They have unit tests that assert what the learner sees: hit, early, late, wrong note, missed.
- Keep audio, MIDI input, and platform APIs behind small adapters at the edge. Use `.web.ts` and `.native.ts` files for platform differences. Business logic never imports a platform module.
- Timing is a product feature. Any claim about latency or sync between audio, visuals, and input must come with a measurement, and you must say what limits the number (device, browser, audio buffer, frame rate).

## Code style

- Bias to deletion and the smallest change that works. No speculative abstractions, one-caller wrappers, or compatibility layers. When replacing an internal API, migrate all callers and delete the old one in the same change.
- Remove dead code before building on top of it.
- TypeScript strict. Make illegal states unrepresentable, use branded types for things like MIDI note numbers and beats, and exhaust union variants with a `never` check.
- Comments only for a non-obvious "why" the code cannot show.
- Fix root causes. A guard is only a fix when the state it handles is legitimate.

## UX rules

- Learner delight beats implementation convenience. Ship fewer, polished features. Every control must earn its place in the core loop: pick a song, practise, get feedback, improve.
- Get the details right: transitions, alignment, feedback on every key press, error and empty states, reduced motion.
- Layouts work from 375px to 1280px on web and on phone and tablet sizes natively. Touch targets suit fingers, and the on-screen keyboard must stay playable.
- Before finishing UI work, run the UX audit skills available in this project.

## Proving it works

- "It compiles" is not done. Verify against the real app: run the web build and drive it in a browser (Playwright is available), and use the Expo simulator tools for iOS and Android once those targets exist. Attach a screenshot or a short note of what you observed.
- For bugs, reproduce first on the same platform, then fix, then show the reproduction now passes. Write the failing test first when a cheap test path exists.
- Tests assert behaviour the learner or the next developer can observe, not internals.
- Break multi-step work into small units that each end in a checked state, and stack commits so the sequence proves itself.
- Every claim in a reply is labelled as measured, inferred, or a guess.

## Autonomy

- Proceed on reversible work and report the result. Pause for anything irreversible: deploys, app store submissions, force-pushes to shared branches, deleting data, messages to real users.
- Give real judgment. If an idea does not earn its place, say so.

## Writing

- Short, plain sentences. No long dashes. Lead with what changes for the learner, then what the next developer inherits.
- PR titles follow Conventional Commits. PR bodies open with a "Why" that restates the goal.
- Never invent links or citations.
