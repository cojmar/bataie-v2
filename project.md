# Battle RPG — Project State & Requirements

## File Index

Only one file exists in this project:

| File | Lines | Purpose |
|------|-------|---------|
| `index.html` | ~2080 | Single-file HTML5 canvas game (all HTML + CSS + JS) |

**Rendering target:** `800x600` logical resolution, scaled to fit the viewport via `viewScale`/`viewOX`/`viewOY`.

---

## Current Project State

### What Works

- ~~**Character selection** — 4 classes (Warrior, Mage, Archer, Magic Gladiator) with carousel UI~~
- ~~**4 skills per class** — S1 (left-click / tap) and S2 (right-click / long-press), S2 has 1s cooldown ring~~
- ~~**Movement** — click ground to walk (with visual marker), player follows path~~
- ~~**Enemy spawning** — 8 enemy types scale with wave; bosses every 5 waves~~
- ~~**Combat** — hit detection, damage, knockback (warrior), AoE, crits, floating damage text~~
- ~~**Wave / environment cycling** — 6 biomes cycle every 5 waves (Forest, Castle, Volcano, Beach, Tundra, Swamp)~~
- ~~**HUD** — HP bar, kill count, wave counter, skill indicators, cooldown ring, death overlay~~
- ~~**Log** — compact combat log with hover/click tooltip showing history~~
- ~~**Touch support** — mobile-friendly with touch events~~
- ~~**Responsive layout** — flexbox with mobile/landscape/orientation breakpoints~~
- ~~**Menu background** — draws current biome colors during character selection~~

### Current Rendering Architecture

The game loop draws in 5 layers (see `gameLoop()` at line 1163):

1. **Layer 1 — Background:** `drawEnvironment()` (sky gradient + flat ground rectangle) + `drawBackgroundParticles()` (40 ambient floating particles above ground line)
2. **Layer 2 — Foliage:** `drawForegroundFoliage()` (45-60 foliage items placed at/near ground line)
3. **Layer 3 — Entities:** `drawEnemies()` → `drawPlayer()` (monsters first, player on top)
4. **Layer 4 — Spells:** `drawProjectiles()` (shockwave, slash, fireball, bolt, arrow, aoe_explosion)
5. **Layer 5 — Effects:** `drawParticles()` + `drawFloatingTexts()`

### Current Environment System (lines 373-381, 1404-1421)

`ENV_CONFIG` defines each biome with flat colors:

```
skyTop  — top of sky gradient
skyMid  — middle of sky gradient
skyBot  — bottom of sky gradient (horizon)
ground  — flat solid color for entire ground area below horizon
groundY — fraction of GAME_H where ground begins (0.78–0.88)
```

This is a **single flat rectangle** for the ground with a 10px gradient transition strip at the horizon line. There is NO depth, NO terrain detail, NO biome-specific ground decoration beyond basic color.

### Current Particle Systems

| System | Location | Purpose |
|--------|----------|---------|
| `bgParticles` (40) | Lines 2002-2015 | Ambient floating particles in sky only (constrained above `groundY - 5`) |
| `particles` | Lines 2016-2026 | Combat/spell/death effect particles (gameplay layer) |
| `fgFoliage` (45-60) | Lines 1445-1464 | Ground decoration items at/near `groundY` |

### Current Foliage System (lines 1441-1544)

`fgFoliage` draws biome-specific ground decoration:
- **Forest:** `drawForestGrass()` — swaying grass blades
- **Volcano:** `drawVolcanoRocks()` — dark rocks with occasional ember glow
- **Castle:** `drawCastleFoliage()` — sparse twigs with small flowers
- **Swamp:** `drawSwampMud()` — mud puddles with occasional bubble particles

These are rendered in **Layer 2** (background layer), below entities. The foliage Y-coordinates are `groundY + (-0.5 to 0.5) * 25`, meaning they straddle the ground line — partially above and partially below.

### Key Constants (lines 350-396)

| Constant | Value | Purpose |
|----------|-------|---------|
| `GAME_W / GAME_H` | 800 / 600 | Logical resolution |
| `MOVE_SPEED` | 2.5 | Player movement speed |
| `MOVE_STOP_DIST` | 3 | Distance threshold to stop moving |
| `GROUND_TOLERANCE` | 30 | Tolerance for determining if click is on ground |
| `DEAD_BODY_LIFETIME` | 2000ms | How long dead bodies persist |
| `s2MaxCooldown` | 1000 (frames at 60fps ≈ 16.7s real) | S2 cooldown duration |

### Key Data Tables

- ~~**CLASS_DATA** (line 357) — 4 classes with stats, colors, skill names~~
- ~~**ENEMY_TYPES** (line 363) — 8 enemy types with HP, ATK, speed, size, color, icon~~
- ~~**ENV_LIST** (line 373) — `['forest','castle','volcano','beach','tundra','swamp']`~~
- ~~**ENV_CONFIG** (line 375) — sky/ground colors per biome~~
- ~~**ENV_DISPLAY** (line 374) — display names~~

---

## Completed Tasks

### 1. ~~Fake Depth Perspective on Ground Area~~ ✅ DONE

### 2. ~~Clear Visual Separation: Background vs Playable Ground~~ ✅ DONE

### 3. ~~Atmospheric Particles Must Stay in Background Only~~ ✅ DONE

### 4. ~~Ground Decoration Per Biome~~ ✅ DONE

### 5. ~~Ground Layer Entity Rules~~ ✅ DONE

### 6. ~~Ground Decoration Rendering Priority~~ ✅ DONE

---

## Active Tasks

> **Workflow:** Tasks are done automatically, one at a time. After finishing, the next starts without confirmation. Progress saved in project.md. No commits during tasks — final review only.

### Task 5 — Mobile-first class info: explain both skills of selected class

**Goal:** Ensure class info section on character selection screen is mobile-first and clearly explains both skills (S1 and S2).

**Details:**
- Skills info is in `.controls-section` div below the stats
- S1 skill: explain what it does and how to trigger it (left-click / tap)
- S2 skill: explain what it does and how to trigger it (right-click / long-press)
- Mobile-first layout: reads well in portrait mode on mobile
- Clean, readable, appropriately sized

**Status:** 🔴 NOT STARTED

---

## Summary

**What exists:** A fully functional 2D side-view arena combat game with 4 classes, 8 enemy types, wave system, 6 biomes, skills, HUD, touch support, and rich environmental theming.

**All tasks completed:**
- [x] Fake depth perspective on ground area
- [x] Clear horizon separation between sky and playable ground
- [x] Atmospheric particles confined to background layer only
- [x] Biome-specific ground surface decoration
- [x] Strict layering system with entity rules
- [x] Proper ground decoration rendering priority

**Active tasks (in order):**
1. [x] Add `info.md` and show gameplay controls/stats on character selection screen
2. [x] Mobile-first layout: works on mobile in portrait and landscape
3. [x] Remove `<h3>⚔️ Select Your Character</h3>` heading
4. [x] Remove duplicate class icon from carousel
5. [ ] Mobile-first class info: explain both skills of selected class

**Workflow:** Tasks are done one at a time. Each task waits for user confirmation before the next starts.
