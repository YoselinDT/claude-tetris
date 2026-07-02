# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

Vanilla JavaScript Tetris. No build step, no package manager, no dependencies — just `index.html`, `style.css`, and `game.js`.

## Running

```bash
open index.html                 # macOS, works directly (no bundler/build)
# or
python3 -m http.server 8000     # then visit http://localhost:8000
```

There is no test suite, linter, or build/package.json in this repo.

## Architecture

Everything lives in `game.js` (single file, no modules). Key pieces:

- **Board model**: `board` is a `ROWS × COLS` matrix; each cell is `0` (empty) or `1–7` (a color index identifying which piece type locked there). `COLORS[]` and `PIECES[]` are parallel arrays indexed by piece type.
- **Piece rotation**: pieces are square matrices; `rotateCW()` does a transpose + row-reverse. `tryRotate()` wraps this with basic wall-kick offsets (`[0, -1, 1, -2, 2]`), trying each until one doesn't collide.
- **Collision** (`collide(shape, ox, oy)`): the single source of truth for whether a shape at a given offset is legal (bounds + overlap with locked cells). Used by movement, rotation, ghost-piece projection, and the drop loop — any change to movement rules should go through this function rather than duplicating bounds checks.
- **Game loop** (`loop(ts)`): driven by `requestAnimationFrame`; accumulates delta time in `dropAccum` and advances the piece one row (or locks it) once `dropAccum >= dropInterval`.
- **Locking a piece**: `lockPiece()` → `merge()` (bake shape into `board`) → `clearLines()` → `spawn()` (promote `next` to `current`, generate a new `next`; if the new piece immediately collides, `endGame()` fires).
- **Scoring/leveling**: `LINE_SCORES = [0, 100, 300, 500, 800]` × `level`. Level increases every 10 lines cleared; `dropInterval = max(100, 1000 - (level-1)*90)` controls fall speed.
- **Rendering**: `draw()` clears and redraws the whole board canvas each frame (grid, locked blocks, ghost piece at `ghostY()` with `globalAlpha = 0.2`, then the current piece). `drawNext()` renders the preview piece to a separate `next-canvas`.
- **Input**: a single `keydown` listener switches on `e.code` (arrows, `KeyX` to rotate, `Space` for hard drop, `KeyP` to pause). Pausing/game-over short-circuits movement handling but not the `P` key.

If you change board dimensions (`COLS`, `ROWS`) or `BLOCK` size, update the `<canvas id="board">` `width`/`height` attributes in `index.html` to match (`COLS × BLOCK`, `ROWS × BLOCK`).
