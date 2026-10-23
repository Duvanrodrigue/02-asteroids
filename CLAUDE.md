# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Asteroids clone in plain HTML5 Canvas. No build step, no dependencies, no package.json, no tests, no linter. README is in Spanish; code comments are also Spanish (e.g. `// Disparar`, `// Nivel completado`) — match that.

## Run

Open `index.html` in a browser, or `npx serve .` and visit `http://localhost:3000`. Reload to see changes.

## Architecture

Everything lives in `game.js`, loaded by `index.html` via a plain `<script>` (no modules, `'use strict'`). The canvas is fixed 800x600 (`W`, `H` constants must match the `<canvas>` attributes in `index.html`).

`game.js` is organised in banner-comment sections (`// ── Input ──`, `Utils`, `Update`, `Draw`):

- **Entities**: classes `Bullet`, `Asteroid`, `Ship`, `Particle`. Each has `update(dt)` and a `dead` flag; dead entities are removed by array `.filter` in `update()`.
- **Global state**: module-level `let` vars (`ship`, `bullets`, `asteroids`, `particles`, `score`, `lives`, `level`) reset by `initGame()`. `state` is a small state machine: `'playing'` → `'dead'` (2s `deadTimer`, then `ship.reset()`) → `'playing'`, or → `'gameover'` (Space restarts via `initGame()`).
- **Loop**: `loop(ts)` computes `dt` in seconds and calls `update(dt)` then `draw()`. All motion must be dt-scaled.
- **Input**: `keys` (held) and `justPressed` (single-frame edge) maps keyed by `KeyboardEvent.code`; read through `pressed(code)`.
- **World**: toroidal — positions wrap with `wrap(v, max)`.
- **Asteroid sizing**: `RADII`, `SPEEDS`, `POINTS` are arrays indexed by size 1–3 (index 0 unused). Large asteroids `split()` into smaller ones on hit. Ship-vs-asteroid collision uses `a.radius * 0.82` as forgiveness.
- **Levels**: clearing all asteroids calls `nextLevel()`, which spawns `3 + level` large asteroids outside a `SAFE_DIST` around screen centre.

The README mentions power-ups and a "shooting star" asteroid type; check `game.js` before assuming they exist.
