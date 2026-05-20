# AGENTS.md — Battle RPG

## Project Overview

Single-file HTML5 canvas game (`index.html`, ~2550 lines). All HTML, CSS, and vanilla JavaScript in one file. No build system, no linting, no test framework. Open `index.html` in a browser to play.

## Build / Run

- **No build step required.** Open `index.html` in any modern browser.
- No lint or test commands exist. Development is done by editing `index.html` directly and refreshing the browser.

## Code Style

### Language & Structure

- **Single file:** Everything lives in `index.html` — `<style>` block for CSS (lines ~7–348), `<script>` block for JS (lines ~407–2548).
- **Vanilla JS only.** No frameworks, no modules, no imports/exports. All functions and variables are in the global scope.
- **Functions use `function` keyword** (not arrow functions), with `function` declared at top-level or nested inside event listeners.

### Naming Conventions

- **Constants:** `UPPER_SNAKE_CASE` for game constants — `GAME_W`, `GAME_H`, `MOVE_SPEED`, `DEAD_BODY_LIFETIME`, `GROUND_TOLERANCE`.
- **Config objects:** `UPPER_SNAKE_CASE` — `CLASS_DATA`, `ENEMY_TYPES`, `ENV_LIST`, `ENV_CONFIG`, `SKILL_DESC`.
- **Global state:** `camelCase` — `gameState`, `selectedClass`, `player`, `enemies`, `projectiles`, `particles`, `floatingTexts`, `kills`, `wave`.
- **Functions:** `camelCase` — `startGame`, `handleLeftClick`, `updatePlayerMovement`, `drawEnvironment`.
- **Loop variables:** Single letter or short — `e` (enemy), `p` (particle/projectile), `i` (index), `dt` (delta time).

### Formatting

- **No formal formatter.** Use 2-space indentation consistently.
- **Line length:** Keep lines under ~100 chars where possible.
- **Semicolons:** Always use semicolons.
- **Quotes:** Single quotes for strings (`'#e74c3c'`, `'PLAYING'`).
- **Comma placement:** No trailing commas.
- **Braces:** Opening brace on same line (K&R style).
- **Comments:** Block comments with `//` for section dividers (e.g., `// ═══ LAYER 1 — BACKGROUND ═══`), inline `//` for brief explanations.

### Types

- No TypeScript or JSDoc. Types are implicit from object shapes:
  - **Player:** `{ x, y, hp, maxHp, atk, spd, color, accent, animTimer, hitFlash, alive, facing, isMoving, moveTargetX, moveTargetY, moveSpeed }`
  - **Enemy:** `{ name, icon, hp, maxHp, atk, speed, color, size, alive, hitFlash, attackTimer, attackCooldown }`
  - **Projectile:** Varies by `type` — `'slash'`, `'bolt'`, `'fireball'`, `'arrow'`, `'shockwave'`, `'aoe_explosion'`
  - **Particle:** `{ x, y, vx, vy, life, maxLife, color, size }`

### Error Handling

- **Silent failures are standard.** Use `catch(()=>{})` for expected failures (fullscreen API).
- **Null/undefined checks:** Early returns with `if (!e.alive) continue;` and `if (gameState !== 'PLAYING') return;`.
- **Boundary clamping:** All positions clamped to game bounds — `Math.max(30, Math.min(GAME_W - 30, x))`.
- **Delta time clamping:** `Math.min((time - lastTime) / 16.67, 3)` prevents spiral of death.

### Game Architecture

- **Rendering order (strict 5 layers):**
  1. Background (sky + ground + bg particles + foliage)
  2. Ground decoration (biome-specific)
  3. Entities (enemies → player → spells on top)
  4. Projectiles/spells
  5. Effects (particles + floating texts)
- **Game loop:** `requestAnimationFrame` → `gameLoop(time)` with delta time normalized to ~60fps (16.67ms).
- **Coordinate system:** 800x600 logical resolution scaled via `viewScale`/`viewOX`/`viewOY`.
- **State machine:** `gameState` in `['MENU', 'PLAYING', 'GAME_OVER']`.

### Canvas Drawing Patterns

- Save/restore context: Always `ctx.save()` before transforms, `ctx.restore()` after.
- Reset transforms between frames: `ctx.setTransform(dpr, 0, 0, dpr, 0, 0)`.
- Reset `globalAlpha` to `1` after any alpha drawing.
- Use `ctx.shadowBlur` / `ctx.shadowColor` sparingly for performance.

## Key Constants & Config

| Constant | Value | Description |
|---|---|---|
| `GAME_W` / `GAME_H` | 800 / 600 | Logical resolution |
| `DEPTH_GROUND_TOP` | 0.55 | Top limit of playable depth zone |
| `MOVE_SPEED` | 2.5 | Player movement speed (dt-scaled) |
| `DEAD_BODY_LIFETIME` | 2000 | ms before dead bodies removed |
| `s2MaxCooldown` | 1000ms | Special skill 2 cooldown |
| `MAX_LOGS` | 12 | Max combat log entries |

## Biomes (cycles every 5 waves)

`forest` → `castle` → `volcano` → `beach` → `tundra` → `swamp`

## Classes

`warrior` (tanky, slow) | `mage` (fast, glass cannon) | `archer` (fastest, crit-focused) | `magicgladiator` (highest HP, AoE damage)

## Current TODO (from `project.md`)

- Staff positioning for Mage (pivot from ground contact point, not hand center)
- SP1 Magic Bolt spawning from crystal tip at current staff position
- SP2 Fireball spawning from glowing orb in left hand
- Staff visual clamping (rotation clamped, no 360-degree rotation)
