# Battle RPG — Project State & Requirements

## File Index

| File | Lines | Purpose |
|------|-------|---------|
| `index.html` | ~2080 | Single-file HTML5 canvas game (all HTML + CSS + JS) |

**Rendering target:** `800x600` logical resolution, scaled via `viewScale`/`viewOX`/`viewOY`.

**Rendering order:** 5 layers — Background → Foliage → Entities → Spells → Effects

**What exists:** Fully functional 2D side-view arena combat game with 4 classes, 8 enemy types, wave system, 6 biomes, skills, HUD, touch support, and rich environmental theming.

## TODO — Mage (Gandalf Wizard) Redesign

### Completed
- [x] Redesign MAGE appearance: Gandalf-style grey robe, wide-brimmed pointed hat, white beard, bushy eyebrows, brown belt with gold buckle
- [x] Add glowing purple orb in off hand (left hand)
- [x] Redesign staff with gold bands, bark texture, metal cap, glowing crystal

### In Progress
- [ ] **Staff positioning:** Staff should pivot from bottom (ground contact point), not from hand center. Bottom stays on ground, top swings from top-left to top-right (no 360° rotation). Staff length should not exceed foot height.
- [ ] **SP1 (Magic Bolt):** Spell must spawn from crystal tip at current staff position, travel toward mouse cursor direction
- [ ] **SP2 (Fireball):** Spell must spawn from glowing orb in off hand (left hand), travel toward mouse cursor direction
- [ ] **Staff visual:** Hand grips staff from middle, crystal at top, base on ground, rotation clamped to prevent flipping
