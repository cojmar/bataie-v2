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

The lower gameplay area (from horizon line downward) presents a **layered depth illusion** while keeping the side-scrolling visual style. Characters move on a ground plane where vertical positioning creates a sense of depth — entities lower on screen appear "closer," entities higher (near horizon) appear "further away."

- [x] Perspective ground plane that visually slopes from horizon to foreground
- [x] Horizontal depth bands/lines on the ground surface that get wider/closer toward the horizon
- [x] Ground Y position visually separates sky background from playable area
- [x] Horizontal grid lines enhance the depth illusion
- [x] Player/enemy shadows and scale reinforce the depth

### 2. ~~Clear Visual Separation: Background vs Playable Ground~~ ✅ DONE

The boundary between sky background and ground gameplay area is **clearly defined** at the horizon line.

- [x] Distinct horizon line / skyline at `groundY` — silhouette of terrain features depending on biome
- [x] Horizon line is a visual anchor that clearly separates "sky" from "ground"
- [x] Atmospheric particles NEVER appear above the ground line in the playable area

### 3. ~~Atmospheric Particles Must Stay in Background Only~~ ✅ DONE

All decorative atmospheric particles (dust motes, fog, fireflies, etc.) exist **only in the sky/background layer** and **never appear on the playable ground layer**.

- [x] `bgParticles` rendering strictly above the ground line
- [x] New ambient effect system confined to the sky area
- [x] Ground-specific effects (mud bubbles, volcanic steam, etc.) belong as **ground decoration**, not atmospheric particles

### 4. ~~Ground Decoration Per Biome~~ ✅ DONE

Each biome has natural ground decoration that matches its environment:

| Biome | Ground Decoration |
|-------|-------------------|
| **Forest** | Soft animated grass movement on the ground surface |
| **Volcano** | Dark volcanic rock terrain texture on the ground |
| **Castle** | Sparse vegetation and small natural details on stone/brick ground |
| **Swamp** | Muddy wet terrain with puddles |

- [x] Ground decoration is **subtle and readable** — enemies and players remain clearly visible during combat
- [x] Decoration enhances depth without interfering with gameplay readability
- [x] Ambient visual effects enhance the atmosphere but stay non-intrusive

### 5. ~~Ground Layer Entity Rules~~ ✅ DONE

Only the following are allowed on the ground layer:
- [x] Players
- [x] Monsters / enemies
- [x] Skills and combat effects (projectiles, AoE, particles from attacks)
- [x] Ground surface details (biome-specific decoration)

Everything else (ambient particles, atmospheric effects, background foliage) stays in the background layer.

### 6. ~~Ground Decoration Rendering Priority~~ ✅ DONE

Ground decoration renders in the correct order:

1. Sky background (gradient)
2. Sky atmospheric particles (above ground line only)
3. **Ground surface** (depth-perspective terrain)
4. Ground decoration (biome-specific, subtle)
5. Dead bodies (fading)
6. Enemies
7. Player
8. Spells/projectiles
9. Combat particles & floating text

Ground decoration is visible but never obscures gameplay entities.

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

The game is visually compelling with depth perspective on the ground plane, proper horizon definition, biome-specific ground surface decoration, and reinforced layer separation.
