# Snake Game — Planning Document

## 1. Overview

A classic Snake game where the player controls a snake that grows longer each time it eats food. The game ends when the snake collides with itself or the boundary walls. The goal is to achieve the highest score possible.

## 2. Game Mechanics

### Core Rules
- The snake moves continuously in the current direction at a fixed tick rate.
- The player changes direction using the arrow keys (up / down / left / right).
- The snake cannot reverse directly into itself (e.g., pressing left while moving right is ignored).
- A piece of food spawns at a random unoccupied cell on the grid.
- When the snake's head reaches the food cell, the snake grows by one segment and a new food item spawns.
- The game ends when the snake's head collides with a wall or with its own body.

### Scoring
- Each food item collected awards **10 points**.
- A running score is displayed on-screen during gameplay.
- The high score persists across sessions (saved to a local JSON file).

### Difficulty Progression
- The snake's speed increases slightly every **5 food items** collected, up to a defined maximum speed.

## 3. Tech Stack

| Layer        | Technology         | Reason                                          |
|--------------|--------------------|--------------------------------------------------|
| Language     | **Python 3.10+**   | Rapid development, large ecosystem               |
| Game library | **pygame 2.x**     | Mature 2-D game library with simple event loop    |
| Data storage | **JSON**           | Lightweight format for persisting high scores     |
| Testing      | **pytest**         | Standard Python test runner                       |
| Linting      | **ruff**           | Fast, all-in-one Python linter and formatter      |

## 4. File Structure

```
snake-game/
├── README.md
├── requirements.txt          # pygame, pytest, ruff
├── docs/
│   ├── plans/
│   │   └── snake-game.md     # this document
│   └── change_log/
├── src/
│   ├── __init__.py
│   ├── main.py               # entry point — initialises pygame and runs the game loop
│   ├── game.py               # Game class — owns state, update/render cycle
│   ├── snake.py              # Snake class — position, direction, growth logic
│   ├── food.py               # Food class — random spawn logic
│   ├── scoreboard.py         # Score tracking and high-score persistence
│   └── constants.py          # grid size, colours, speeds, FPS, etc.
└── tests/
    ├── __init__.py
    ├── test_snake.py
    ├── test_food.py
    ├── test_game.py
    └── test_scoreboard.py
```

## 5. Key Classes

### `Snake`
- `body: list[tuple[int, int]]` — ordered list of (x, y) grid positions.
- `direction: tuple[int, int]` — current movement vector.
- `grow()` — flag the snake to add a segment on the next move.
- `move()` — advance the head and optionally drop the tail.
- `check_collision() -> bool` — wall and self-collision detection.

### `Food`
- `position: tuple[int, int]` — current grid cell.
- `respawn(occupied: set)` — pick a new random cell that is not occupied by the snake.

### `Game`
- Owns a `Snake`, a `Food`, and a `Scoreboard`.
- `update()` — advance one tick: move snake, check collisions, check food pickup.
- `render(surface)` — draw the grid, snake, food, and score.
- `handle_input(event)` — translate key presses into direction changes.

### `Scoreboard`
- `score: int` — current session score.
- `high_score: int` — loaded from / saved to `high_score.json`.
- `increment(points: int)` — add points and update high score if exceeded.

## 6. Milestones

### Milestone 1 — Minimum Viable Game
- [ ] Set up project skeleton (`requirements.txt`, directory layout).
- [ ] Implement `constants.py` with grid dimensions, colours, and speeds.
- [ ] Implement `Snake` class with movement and self-collision.
- [ ] Implement `Food` class with random spawning.
- [ ] Implement core `Game` loop (update → render → input).
- [ ] Display the snake and food on a pygame window.

### Milestone 2 — Scoring & Polish
- [ ] Implement `Scoreboard` with on-screen score display.
- [ ] Persist high score to `high_score.json`.
- [ ] Add difficulty progression (speed ramp).
- [ ] Add a game-over screen with restart option.

### Milestone 3 — Testing & Quality
- [ ] Write unit tests for `Snake` (movement, growth, collision).
- [ ] Write unit tests for `Food` (respawn avoids snake body).
- [ ] Write unit tests for `Scoreboard` (increment, persistence).
- [ ] Add integration test for a short automated game sequence.
- [ ] Configure `ruff` and ensure zero lint warnings.

### Milestone 4 — Stretch Goals (optional)
- [ ] Add sound effects (eat, game-over).
- [ ] Add a start/menu screen.
- [ ] Support grid wrapping mode (snake exits one side, enters the other).
- [ ] Add obstacles / walls inside the grid.
