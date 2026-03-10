# Snake Game - Planning Document

> **Status:** Draft
> **Created:** 2026-03-10
> **Stack:** TypeScript, Vite 6, HTML5 Canvas

---

## 1. Overview

A lightweight, browser-based Snake game. The player controls a snake that grows by eating food. The game ends when the snake collides with itself or the walls. No frameworks, no heavy dependencies — just TypeScript rendered to a `<canvas>` element, bundled with Vite.

## 2. Goals

- **Lightweight** — zero runtime dependencies; total bundle < 20 KB gzipped.
- **Easy to play** — keyboard (arrow keys / WASD) and swipe (mobile touch) controls.
- **Fast to build** — single-page app, no routing, no state library.
- **Modern tooling** — Vite 6 (2026 LTS), TypeScript 5.7+, Node 22 LTS.

## 3. Tech Stack & Dependencies

| Tool | Version | Purpose |
|------|---------|---------|
| Node.js | 22 LTS | Runtime |
| Vite | ^6.x | Dev server & bundler |
| TypeScript | ^5.7 | Language |

No additional runtime libraries. Dev-only: `vite`, `typescript`.

## 4. Project Structure

```
snake-game/
├── index.html            # Entry point, hosts <canvas>
├── src/
│   ├── main.ts           # Bootstrap: init canvas, start game loop
│   ├── game.ts           # Game class — loop, state machine, score
│   ├── snake.ts          # Snake model — segments, direction, grow/move
│   ├── food.ts           # Food model — random placement, collision check
│   ├── renderer.ts       # Canvas drawing — grid, snake, food, score HUD
│   ├── input.ts          # Input handler — keyboard + touch events
│   ├── constants.ts      # Grid size, tick rate, colors, etc.
│   └── types.ts          # Shared types (Point, Direction, GameState)
├── public/
│   └── favicon.svg       # Simple snake icon
├── package.json
├── tsconfig.json
├── vite.config.ts
└── docs/
    └── plans/
        └── snake-game.md # This file
```

## 5. Core Types

```ts
// types.ts
export type Direction = "UP" | "DOWN" | "LEFT" | "RIGHT";

export interface Point {
  x: number; // column index (0-based)
  y: number; // row index (0-based)
}

export enum GameState {
  Ready,    // waiting for first keypress
  Playing,
  Paused,
  GameOver,
}
```

## 6. Game Mechanics

| Aspect | Detail |
|--------|--------|
| Grid | 20 x 20 cells (configurable via `constants.ts`) |
| Tick rate | 150 ms base interval; decreases slightly as score rises |
| Snake start | Length 3, centered, facing RIGHT |
| Food | One piece at a time; spawns on a random empty cell |
| Growth | Snake grows by 1 segment each time it eats food |
| Collision | Wall collision OR self collision = Game Over |
| Score | +10 per food eaten; displayed in-canvas HUD |
| Restart | Press Enter or tap screen on Game Over to restart |
| Pause | Press Space to toggle pause |

## 7. Module Responsibilities

### `main.ts`
- Query the `<canvas>` element, set its size.
- Instantiate `Game` and call `game.start()`.

### `game.ts` — `Game` class
- Owns the game loop (`requestAnimationFrame` + tick accumulator).
- Holds `Snake`, `Food`, `Renderer`, `InputHandler` instances.
- State machine: Ready -> Playing -> (Paused | GameOver) -> Ready.
- Each tick: read buffered input, update snake, check collisions, maybe spawn food, increment score, request render.

### `snake.ts` — `Snake` class
- `segments: Point[]` — head is `segments[0]`.
- `direction: Direction` — current heading.
- `move()` — shift segments forward; pop tail unless growing.
- `grow()` — flag next move to skip tail pop.
- `checkSelfCollision(): boolean`.
- `setDirection(d: Direction)` — ignore reverse (can't go LEFT if heading RIGHT).

### `food.ts` — `Food` class
- `position: Point`.
- `respawn(occupied: Point[])` — pick a random cell not in `occupied`.

### `renderer.ts` — `Renderer` class
- `drawGrid()` — subtle grid lines.
- `drawSnake(segments)` — head in accent color, body in main color.
- `drawFood(position)` — small filled circle or rounded rect.
- `drawScore(score)` — top-left overlay text.
- `drawOverlay(text)` — semi-transparent overlay for Ready / Paused / Game Over screens.
- `clear()` — fill background.

### `input.ts` — `InputHandler` class
- Listens for `keydown` (arrow keys, WASD, Space, Enter).
- Listens for `touchstart` / `touchend` for swipe detection.
- Exposes a queue: `popDirection(): Direction | null` to decouple input from tick rate.
- Exposes `onAction(callback)` for pause/restart signals.

### `constants.ts`
```ts
export const GRID_SIZE = 20;
export const CELL_PX = 24;          // pixel size of one cell
export const BASE_TICK_MS = 150;
export const MIN_TICK_MS = 80;
export const SCORE_PER_FOOD = 10;
export const COLORS = {
  bg: "#1a1a2e",
  grid: "#16213e",
  snakeHead: "#e94560",
  snakeBody: "#0f3460",
  food: "#e94560",
  text: "#eaeaea",
  overlay: "rgba(0,0,0,0.6)",
};
```

## 8. Rendering Approach

- Single `<canvas>` element sized to `GRID_SIZE * CELL_PX` (480 x 480 px default).
- `requestAnimationFrame` drives the loop; a tick accumulator ensures fixed-step game logic independent of frame rate.
- Renderer clears and redraws every frame (simple; no partial updates needed at this scale).

## 9. Mobile / Touch Support

- Swipe detection: compare `touchstart` and `touchend` positions; map dominant axis delta to a `Direction`.
- Canvas is responsive: CSS `max-width: 100vw; max-height: 100vh; aspect-ratio: 1;` with `object-fit: contain`.
- A visible "Tap to start" prompt replaces "Press Enter" on touch devices.

## 10. Build & Run

```bash
# Install
npm create vite@latest snake-game -- --template vanilla-ts
cd snake-game
npm install

# Dev
npm run dev        # localhost:5173

# Build
npm run build      # outputs to dist/
npm run preview    # preview production build
```

## 11. Future Enhancements (Out of Scope for v1)

- High-score persistence (localStorage).
- Speed level selector.
- Sound effects (Web Audio API).
- Multiplayer (WebSocket).
- PWA offline support.

## 12. Implementation Order

1. Scaffold project with Vite + TS.
2. Create `constants.ts` and `types.ts`.
3. Implement `Snake` class with unit-testable `move` / `grow` / `collision` logic.
4. Implement `Food` class.
5. Implement `Renderer` — get something visible on screen.
6. Implement `InputHandler`.
7. Wire everything together in `Game` + `main.ts`.
8. Add mobile touch support.
9. Polish: overlay screens, score HUD, speed ramp.
10. Final review, bundle size check, deploy.
