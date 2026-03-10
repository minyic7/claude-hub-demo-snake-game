# Snake Game — Planning Document

## Overview

A classic Snake game built as a web application. The player controls a snake that moves around a grid, eating food to grow longer while avoiding collisions with walls and its own body.

## Tech Stack

- **Language:** JavaScript (vanilla, no frameworks)
- **Rendering:** HTML5 Canvas
- **Styling:** CSS3
- **Build:** No build step required — runs directly in the browser

## Game Mechanics

### Core Rules
1. The snake moves continuously in the current direction at a fixed tick rate.
2. The player changes direction using arrow keys (or WASD).
3. A food item spawns at a random empty cell on the grid.
4. When the snake's head reaches the food, the snake grows by one segment and the score increments.
5. The game ends when the snake collides with a wall or its own body.

### Grid & Sizing
- Default grid: **20 × 20** cells.
- Cell size: **20 px** (canvas = 400 × 400 px).
- Both values configurable via constants.

### Speed & Difficulty
- Starting tick interval: **150 ms**.
- Every **5 points**, the interval decreases by **10 ms** (minimum **60 ms**).

## Architecture

```
index.html          — Entry point, canvas element, score display
css/
  style.css         — Layout, colors, game-over overlay
js/
  main.js           — Initialization, game loop, event listeners
  snake.js          — Snake class (position, direction, growth, collision)
  food.js           — Food class (random spawn, avoid snake)
  renderer.js       — Drawing logic (grid, snake segments, food)
  constants.js      — Grid size, cell size, colors, speed settings
```

### Key Classes / Modules

| Module | Responsibility |
|---|---|
| `Snake` | Stores segment positions, moves each tick, grows on eat, detects self-collision |
| `Food` | Picks a random empty cell, exposes position |
| `Renderer` | Clears canvas, draws grid lines, snake, and food each frame |
| `Game` (in main.js) | Owns the game loop (`setInterval`), handles input, checks wall/self collision, manages score and game-over state |

## Features

### MVP (Phase 1)
- [ ] Render a grid on an HTML5 canvas
- [ ] Snake movement with arrow-key input
- [ ] Food spawning and eating (score tracking)
- [ ] Collision detection (walls + self)
- [ ] Game-over screen with final score
- [ ] Restart button

### Enhancements (Phase 2)
- [ ] High-score persistence (`localStorage`)
- [ ] Pause / resume (Space key)
- [ ] Mobile touch controls (swipe)
- [ ] Sound effects (eat, game-over)
- [ ] Visual polish: smooth segment animation, gradient snake color

### Stretch Goals (Phase 3)
- [ ] Multiple difficulty presets (Easy / Medium / Hard)
- [ ] Wrap-around mode (no wall collision)
- [ ] Obstacles / maze walls
- [ ] Multiplayer (two snakes, split keyboard)

## File Descriptions

| File | Purpose |
|---|---|
| `index.html` | Minimal HTML: a `<canvas>`, a score `<div>`, and a restart `<button>`. Loads CSS and JS. |
| `css/style.css` | Centers the canvas, styles the score display, and provides a semi-transparent game-over overlay. |
| `js/constants.js` | Exports grid dimensions, cell pixel size, initial speed, speed increment rules, and color palette. |
| `js/snake.js` | `Snake` class — `move()`, `grow()`, `checkSelfCollision()`, `getHead()`, `getSegments()`. |
| `js/food.js` | `Food` class — `spawn(occupiedCells)`, `getPosition()`. |
| `js/renderer.js` | `Renderer` class — `drawGrid()`, `drawSnake(segments)`, `drawFood(position)`, `clear()`. |
| `js/main.js` | Creates instances, starts `setInterval` loop, listens for `keydown`, checks collisions, updates score, shows game-over overlay. |

## Input Handling

- **Arrow keys** or **WASD** change direction.
- Opposite-direction input is ignored (can't reverse into yourself).
- **Space** toggles pause (Phase 2).
- Direction is queued so rapid key presses between ticks don't skip directions.

## Testing Plan

- Unit tests for `Snake` movement and collision logic (can use any lightweight test runner).
- Manual playtesting for rendering, input responsiveness, and edge cases (eating food at the border, full-grid win condition).

## Open Questions

1. Should the snake wrap around the edges instead of dying? → Deferred to Phase 3 as an option.
2. Should we add a leaderboard beyond local high score? → Out of scope for now.
