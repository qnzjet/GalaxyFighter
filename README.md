# 🛸 Galaxy Fighter (PC • HTML5 • Single File)

A low-res, black-and-white, offline HTML5 canvas shooter.  
Designed for **PC keyboards** only — no touch/gamepad by default.

- **Fast, lightweight:** single HTML file, no builds or dependencies.
- **Retro vibe:** pixelated outer-space starfield, vector ships, crunchy feel.
- **Progression:** levels, score multiplier, and a **boss every 10th level**.
- **Pickups:** Missiles (**M**), Seekers (**S**), Multi-shot (**3**).
- **Tunable:** live settings for spawn rate, enemy cap, and ultra-lo-fi mode.
- **Persistence:** localStorage for high score and settings.

---

## 🚀 Quick Start

1. Download `index.html` (the game file).
2. Double-click to open in your desktop browser (Chrome/Edge/Firefox/Safari).
3. **Enter** to start • **Space** to fire • **W/S** or **↑/↓** to move
4. Use the **Settings** row to tweak difficulty/performance on the fly.

> 💡 No server needed; runs locally. If your browser blocks localStorage in
> file URLs, allow storage for `file://` or run a tiny local server.

---

## 🎮 Controls (PC only)

- **Move:** `W/S` or `↑/↓`  
- **Fire:** `Space`  
- **Start / Restart:** `Enter`  
- **Pause:** Button in UI (or edit code to bind `P`)

---

## 🧩 Gameplay

- **Levels & Multiplier:** Each level raises a **score multiplier** (up to **10×**).
- **Bosses:** Every **10th level** spawns a large UFO boss:
  - Rotating patterns: **fan**, **spread**, **laser sweep**
  - HP bar displayed above the boss
- **Pickups (Power-Ups):** dropped by regular enemies (~25% chance)
  - **Missile (M):** slow, heavy damage projectiles
  - **Seeker (S):** bullets with light homing toward the nearest foe
  - **Multi-shot (3):** three-way spread
  - Power-ups are **timed**; after the timer ends you return to **PEW** (default)

---

## 🛠 Settings (live & persisted)

Below the canvas you’ll find a **Settings** row:

- **Spawn Rate** (slider): spawn cadence in ticks  
- **Max Enemies** (number): hard cap on active enemies  
- **Ultra-Lo-Fi** (checkbox): halves the resolution for extra performance

All three are saved in `localStorage` and loaded at boot.

---

## 🖼 Aesthetic & Performance

- **Canvas size:** default **480×320**, **Ultra-Lo-Fi** **240×160**
- **Look:** monochrome lines & boxes for ship/aliens; pixelated outer-space starfield
- **Perf tips:**
  - Lower spawn rate / max enemies
  - Toggle Ultra-Lo-Fi (great on low-end laptops)
  - Keep the tab focused (background tabs throttle animation)

---

## 🧱 Architecture Overview

Although it’s a **single file**, the code is structured like a tiny engine:

- **Main loop:** `tick()` updates → `draw()` renders (via `requestAnimationFrame`)
- **Systems:** starfield, input, player, enemies, bullets, power-ups, collisions, HUD
- **State:** a single `game` object stores runtime state  
- **Persistence:** `localStorage` stores `high score` and `settings`

### Core Entities

- **Player:** `{ x, y, lives, cd, weapon, weaponTimer }`
- **Enemy:** `{ x, y, vx, vy, hp, boss?, hpMax?, pattern? }`
- **Bullet (player/enemy):** `{ x, y, vx, vy, dmg, kind }`
- **Power-up:** `{ x, y, vx, type }`
- **Stars:** `{ x, y, s, layer }`

---

## 🔄 Game Loop (Flow)

```mermaid
flowchart TD
  A[Start / Enter] --> B[Init State & Stars]
  B --> C[Main Loop: tick()]
  C --> D[Read Input (W/S, Space)]
  D --> E[Update Player (fire, cooldown)]
  E --> F[Update Bullets (incl. seekers)]
  F --> G[Update Enemies & Boss AI]
  G --> H[Update Enemy Bullets]
  H --> I[Update Power-ups]
  I --> J[Collisions]
  J --> K{Enemy killed?}
  K -->|Yes| L[Score + Drops + LevelKills]
  L --> M{Level or Boss trigger?}
  M -->|Boss every 10th| N[Spawn Boss + Banner]
  M -->|Else| O[Next Level + Multiplier++]
  K -->|No| P[Continue]
  P --> Q[Draw (stars, ships, HUD)]
  Q --> C
  J --> R{Player dead?}
  R -->|Yes| S[Game Over + Save HiScore]
  S --> T[Wait for Enter]
