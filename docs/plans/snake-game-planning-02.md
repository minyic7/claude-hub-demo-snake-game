# Snake Game — Planning Document 02

## 1. Overview

Build a classic Snake game as a browser-based application. The player controls a snake that moves on a grid, eats food to grow longer, and must avoid colliding with itself or the walls. The game ends when a collision occurs.

## 2. Technology Stack

| Layer        | Choice              | Rationale                                      |
| ------------ | ------------------- | ---------------------------------------------- |
| Language     | JavaScript (ES6+)   | No build step needed; runs natively in browsers |
| Rendering    | HTML5 Canvas        | Smooth, frame-based rendering ideal for games  |
| Styling      | CSS3                | Minimal UI chrome around the canvas            |
| Bundling     | None (single-page)  | Keep the project simple and dependency-free    |

## 3. Project Structure

```
snake-game/
├── index.html          # Entry point, hosts the canvas and UI
├── css/
│   └── style.css       # Layout, colors, game-over overlay
├── js/
│   ├── main.js         # Initializes the game loop and event listeners
│   ├── game.js         # Core game state machine (init, running, paused, over)
│   ├── snake.js        # Snake entity: position, direction, growth logic
│   ├── food.js         # Food spawning and collision detection
│   ├── renderer.js     # Canvas drawing routines (grid, snake, food, score)
│   └── input.js        # Keyboard input handler with direction queue
├── docs/
│   ├── plans/          # Planning documents
│   └── change_log/     # Agent change logs
└── README.md
```

## 4. Game Mechanics

### 4.1 Grid & Canvas

- The play area is a **20 × 20** cell grid.
- Each cell is **20 px**, giving a **400 × 400 px** canvas.
- The grid is drawn with subtle lines to aid visibility.

### 4.2 Snake

- Starts at center of the grid, length 3, moving right.
- Stored as an ordered array of `{x, y}` segments (head-first).
- Each tick the head advances one cell in the current direction; the tail is removed unless the snake just ate food.
- Direction changes are queued so that rapid key presses within a single tick don't cause self-collision.

### 4.3 Food

- One food item on the grid at a time.
- Spawns at a random empty cell (not occupied by the snake).
- When eaten: score increments, snake grows by one segment, new food spawns.

### 4.4 Collision Detection

- **Wall collision**: head `x` or `y` is outside `[0, gridSize)`.
- **Self collision**: head position matches any other segment.
- Either collision triggers the **Game Over** state.

### 4.5 Scoring

- **+10 points** per food eaten.
- High score persisted in `localStorage`.

## 5. Game Loop

```
┌─────────────┐
│   INIT      │  (show "Press SPACE to start")
└─────┬───────┘
      │ SPACE
      v
┌─────────────┐
│   RUNNING   │◄──── SPACE (unpause)
└─────┬───┬───┘
      │   │ SPACE
      │   v
      │ ┌───────────┐
      │ │  PAUSED   │
      │ └───────────┘
      │
      │ collision
      v
┌─────────────┐
│  GAME OVER  │  (show score, "Press ENTER to restart")
└─────────────┘
```

- Uses `requestAnimationFrame` with a tick interval (~150 ms base speed).
- Speed increases slightly as the score grows (minimum tick interval: 80 ms).

## 6. Input Handling

| Key              | Action                  |
| ---------------- | ----------------------- |
| Arrow keys / WASD | Change snake direction  |
| Space            | Start / Pause / Unpause |
| Enter            | Restart after game over |

- Opposite-direction inputs are ignored (can't reverse into yourself).
- A direction queue (max depth 2) prevents frame-skip self-collisions.

## 7. Rendering Details

- **Background**: dark charcoal (`#1a1a2e`).
- **Grid lines**: subtle (`rgba(255,255,255,0.05)`).
- **Snake head**: bright green (`#00ff88`).
- **Snake body**: gradient from green to darker green.
- **Food**: red circle (`#ff4757`) with a subtle glow.
- **Score**: displayed above the canvas in a monospace font.
- **Game Over overlay**: semi-transparent dark overlay with final score and high score.

## 8. Implementation Phases

### Phase 1 — Core (MVP)
1. Set up `index.html` with canvas and basic CSS.
2. Implement `snake.js` (movement, growth, direction queue).
3. Implement `food.js` (spawn, collision check).
4. Implement `game.js` (state machine, tick loop).
5. Implement `renderer.js` (draw grid, snake, food, score).
6. Implement `input.js` (keyboard listener, direction queue).
7. Wire everything together in `main.js`.

### Phase 2 — Polish
1. Add pause/unpause functionality.
2. Add game-over overlay with score display.
3. Persist high score in `localStorage`.
4. Add speed ramp as score increases.
5. Improve visuals (gradient snake body, food glow effect).

### Phase 3 — Stretch Goals
1. Mobile touch controls (swipe detection).
2. Sound effects (eat, game over).
3. Wrap-around mode (snake exits one wall, enters opposite).
4. Multiple difficulty levels (grid size / speed presets).

## 9. Testing Strategy

- **Manual playtesting** for core gameplay feel and responsiveness.
- **Unit tests** (optional, using a lightweight test runner) for:
  - Snake movement and growth logic.
  - Collision detection edge cases.
  - Food spawn never overlaps the snake.
  - Direction queue rejects opposite directions.

## 10. Open Questions

- Should we support mobile/touch from the start or defer to Phase 3?
- Should the grid wrap around or use hard walls by default?
- Any preference on additional visual themes?
