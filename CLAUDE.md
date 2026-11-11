# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Vanilla JavaScript Tetris on HTML5 Canvas. No `package.json`, bundler, linter or tests. The README is in Spanish, as is some of the UI text.

## Running

Open `index.html` in a browser, or serve the directory statically (`python3 -m http.server 8000`, then open `http://localhost:8000`).

## Architecture

Three files:
- `index.html` has the 300×600 `#board` canvas, the 120×120 `#next-canvas`, the HUD elements (`#score`, `#lines`, `#level`) and the `#overlay` used for both pause and game over.
- `style.css` is the dark/retro theme.
- `game.js` is a single script that uses globals, with no modules. It looks up DOM elements by id at load time and calls `init()` at the bottom.

`game.js` holds:
- State: the board as a `ROWS×COLS` matrix of 0 or a color index 1–7. `PIECES` and `COLORS` share those indices, and pieces are square matrices.
- Game flow: `init → spawn → loop` (`requestAnimationFrame`, drops a piece when accumulated time reaches `dropInterval`). `lockPiece` runs `merge` and `clearLines`, then `spawn`. A spawn that collides calls `endGame`.
- Rotation: `rotateCW` produces the rotated matrix and `tryRotate` applies wall kicks of ±1 and ±2 columns.
- Scoring and speed: `LINE_SCORES` is multiplied by level. Hard drop gives 2 points per cell and soft drop gives 1 per row. The level goes up every 10 lines. The drop interval is `max(100, 1000 − (level−1)×90)` ms.

## Gotchas

- The canvas `width`/`height` in `index.html` must equal `COLS×BLOCK` by `ROWS×BLOCK`. Change them together with the constants in `game.js`.
- The `#next-canvas` size is fixed in `index.html`, independent of `BLOCK`.
- The controls list is duplicated in `index.html` (the panel) and in the `keydown` handler in `game.js`. Keep them in sync.
