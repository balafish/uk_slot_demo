# CLAUDE.md

## Project Overview

**uk_slot_demo** — An immersive 3D casino lobby web application designed to feel like a native game app, not a traditional web page. Built with Three.js for real-time 3D rendering and GSAP for UI animations.

## Repository Structure

```
uk_slot_demo/
├── index.html                  # 3D Casino Lobby MVP (Three.js + HUD overlay)
├── vegas-casino-universe.html  # Legacy 2D lobby (deprecated, for reference only)
├── README.md
└── CLAUDE.md
```

## Tech Stack

| Technology | Purpose |
|---|---|
| **Three.js** (r162) | 3D scene rendering (WebGL) |
| **UnrealBloomPass** | Post-processing glow/bloom effects |
| **GSAP** (3.12.5) | Camera transitions, UI animations |
| **Vanilla JS** (ES Modules) | Application logic |
| **HTML/CSS** | HUD overlay with glassmorphism |

All libraries loaded via CDN (importmap for Three.js, script tag for GSAP). No build system or bundler required.

## Running the Application

This is a static web app. Serve from any static file server:

```sh
# Option 1: Python
python3 -m http.server 8000

# Option 2: Node.js (npx)
npx serve .

# Option 3: GitHub Pages
# Push to main → Settings → Pages → deploy from main branch
# Access at: https://<username>.github.io/uk_slot_demo/
```

Open `index.html` in browser. No install or build step needed.

## Architecture

### Design Principles (from design spec)

1. **Lobby = Game World**, not a web page. No scrolling — fixed viewport with scene transitions.
2. **Three Layer UI**: Layer 1 (3D world scene) → Layer 2 (interactive building entrances) → Layer 3 (HUD overlay)
3. **Scene-based navigation**: Game entries are 3D locations (Slot Hall, Live Stage, Fishing Harbor, Jackpot Temple), not card grids
4. **Native app feel**: Glassmorphism HUD, micro-interactions, particle effects, animated transitions

### Scene Architecture

```
┌─────────────────────────────────────────┐
│  HUD Top (HTML overlay, z-index: 100)   │  ← Balance, Jackpot ticker, Avatar
├─────────────────────────────────────────┤
│                                         │
│        Three.js WebGL Canvas            │  ← 3D buildings, particles, lighting
│    ┌───────────────────────────────┐    │
│    │  3D Lobby Scene               │    │
│    │  ├── Ground (reflective)      │    │
│    │  ├── Starfield                │    │
│    │  ├── Gold dust particles      │    │
│    │  ├── Slot Hall (left)         │    │
│    │  ├── Live Stage (right)       │    │
│    │  ├── Fishing Harbor (back-L)  │    │
│    │  └── Jackpot Temple (back-C)  │    │
│    └───────────────────────────────┘    │
│                          ┌──────────┐   │
│                          │Zone Panel│   │  ← Slide-in panel (HTML overlay)
│                          └──────────┘   │
├─────────────────────────────────────────┤
│  HUD Bottom (HTML overlay, z-index:100) │  ← Navigation tabs
└─────────────────────────────────────────┘
```

### Camera States

Camera transitions between fixed positions using GSAP:
- **Overview**: Elevated view showing all 4 building entrances
- **Zone views**: Camera flies closer to selected building, zone panel slides in

### Key Code Sections (index.html)

| Section | Description |
|---|---|
| CSS styles | HUD glassmorphism, zone panel, labels, responsive breakpoints |
| `COLORS` / `BUILDING_POS` / `CAM_STATES` | Configuration constants |
| `ZONE_CONTENT` | Content data for each zone's slide-in panel |
| `createSlotHall()` etc. | Building factory functions (3D geometry + lights) |
| Raycaster interaction | Hover scale + click-to-enter zone |
| `transitionCamera()` | GSAP-driven camera movement |
| `enterZone()` / `exitZone()` | Zone navigation logic |
| Animation loop | Particles, animated elements, bloom rendering |

## Development Phases (Roadmap)

- [x] **Phase 1-3 MVP**: 3D scene + 4 building entrances + HUD + zone panels
- [ ] **Phase 4**: Avatar system (3D character, idle animation, teleport)
- [ ] **Phase 5**: Jackpot effects (particle burst, number animations), mission system
- [ ] **Phase 6**: Social features (chat bubbles, floating messages, player presence)
- [ ] **Phase 7**: Micro-interactions (button bounce, ripple, sound, haptics)

## Conventions

### Code Style
- Vanilla JS with ES modules (import/export)
- Single-file MVP; refactor to multi-file when complexity warrants it
- CSS variables for design tokens
- GSAP for all animated transitions (not CSS transitions for camera/complex motion)

### 3D Building Construction
- Use Three.js primitive geometries (BoxGeometry, CylinderGeometry, etc.)
- Emissive materials with `toneMapped: false` for bloom glow
- Each building is a `THREE.Group` with `userData.id` for raycasting
- Point lights per building for colored ambient pools

### HUD / UI
- HTML overlay positioned on top of WebGL canvas
- Glassmorphism: `backdrop-filter: blur()` + semi-transparent backgrounds
- Gold (#ffd700) as primary accent color
- Orbitron font for numbers/titles, DM Sans for body text

## AI Assistant Guidelines

1. **Read before editing** — always read files before modifying.
2. **Keep changes minimal** — only make directly requested changes.
3. **Update this file** — when adding dependencies, new files, or architectural changes.
4. **Respect the design spec** — Lobby = game world, not a web page. No scroll layouts. No card grids for game entries.
5. **Test visually** — 3D changes need browser testing. Describe what you changed so the user can verify.
6. **Performance matters** — limit draw calls, use instancing for repeated geometry, keep particle counts reasonable.
7. **No over-engineering** — prefer simple, direct implementations.
