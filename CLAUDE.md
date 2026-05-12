veritas

# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running the games

No build step. Open any HTML file directly in a browser:

```bash
open shooter.html
open tictactoe.html
```

There are no dependencies, servers, or package managers involved.

## Git workflow

After every feature or meaningful change: commit with a clean message and push.

```bash
git add <files>
git commit -m "imperative-mood summary"
git push
```

Remote: `https://github.com/Ash24X/ClaudeCodeTest` (branch `main`)

## Architecture

All games are **single self-contained HTML files** — CSS in `<style>`, logic in `<script>`, no external resources. This is intentional; keep new games to the same pattern.

### shooter.html — Void Shooter

**State machine**: `S = { MENU, WAVE_BANNER, PLAYING, GAME_OVER }`. The `loop()` function calls `update(dt)` only in `PLAYING` and `WAVE_BANNER` states, then always calls `draw()`.

**Tuning constants**: All gameplay numbers live in `CFG` (player speed, bullet speed, shoot cooldown, etc.) and `ENEMY_DEF` (per-type radius, HP, speed, score, unlock wave). Change these to rebalance without hunting through logic.

**Wave system**: `nextWave()` builds a `spawnQueue` array of enemy-type strings, then `update()` drains it one enemy at a time on a `spawnInterval` timer. A wave ends when `spawnIdx >= spawnQueue.length && enemies.length === 0`.

**Collision**: All entities are circles. `overlap(ax,ay,ar, bx,by,br)` compares squared distance against squared sum of radii — no `Math.sqrt`. Enemy–bullet and enemy–player checks both iterate arrays backward so `splice` is safe mid-loop.

**Persistence**: High score stored in `localStorage` under key `vs_hi`.

**Rendering**: `draw()` layers background → grid → enemies (with HP bar for `maxHp > 1`) → bullets (with `shadowBlur` glow) → player body + barrel → HUD → wave banner. Canvas is resized to full viewport on load and on `resize`.

### tictactoe.html — Tic Tac Toe

**Board**: flat 9-element array. `checkWinner()` tests all 8 win combos from the `WINS` constant.

**AI**: `bestMove()` uses a simple priority — win if possible, block X if possible, then prefer center → corners → edges. Not minimax; intentionally beatable.

**Modes**: `vsComputer` bool gates the AI path. Switching modes resets the score object `{ X, O, D }`.

## Design conventions

- **Color palette** (shared across games): `#e94560` red/accent, `#a8dadc` teal/secondary, `#1a1a2e` / `#0d0d1a` dark backgrounds, `#16213e` / `#0f3460` card/panel backgrounds.
- **No frameworks, no modules** — vanilla JS only, everything in one `<script>` block.
- **`dt`-based movement** in shooter (seconds since last frame, capped at 0.05s) so speed is frame-rate independent.
- New enemy types: add an entry to `ENEMY_DEF` with `{ r, hp, speed, color, outline, score, wave }` — no other code changes needed for spawning or collision.
