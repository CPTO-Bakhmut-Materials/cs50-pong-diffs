# Pong Remake — Step-by-Step Guide

A walkthrough of the [games50/pong](https://github.com/games50/pong) project (CS50 2D, LÖVE2D), from an empty window (`pong-0`) to the finished game (`pong-final`).

Each file describes **one step** between two versions and contains:

1. **General task description** — what this version adds and why.
2. **Small steps** — how to get from the previous version to this one, with code.

Third-party libraries and assets (`push.lua`, `class.lua`, `font.ttf`, `sounds/*.wav`) are only **included** — copy them from the repo; you don't write them yourself.

## Steps

| # | File | Version | What it adds |
|---|---|---|---|
| 1 | [diff-0-1.md](diff-0-1.md) | pong-1 — "The Low-Res Update" | Virtual resolution with the `push` library, sharp pixels, quit with Escape |
| 2 | [diff-1-2.md](diff-1-2.md) | pong-2 — "The Rectangle Update" | Retro font, background color, paddles and ball drawn as rectangles |
| 3 | [diff-2-3.md](diff-2-3.md) | pong-3 — "The Paddle Update" | Paddles move with W/S and ↑/↓, score display |
| 4 | [diff-3-4.md](diff-3-4.md) | pong-4 — "The Ball Update" | Moving ball, `start`/`play` game states, paddles stay on screen |
| 5 | [diff-4-5.md](diff-4-5.md) | pong-5 — "The Class Update" | Refactor into `Paddle` and `Ball` classes (`class.lua` library) |
| 6 | [diff-5-6.md](diff-5-6.md) | pong-6 — "The FPS Update" | Window title, score returns, FPS counter, collision check method |
| 7 | [diff-6-7.md](diff-6-7.md) | pong-7 — "The Collision Update" | Ball bounces off paddles and walls |
| 8 | [diff-7-8.md](diff-7-8.md) | pong-8 — "The Score Update" | Points are scored when the ball leaves the screen |
| 9 | [diff-8-9.md](diff-8-9.md) | pong-9 — "The Serve Update" | `serve` state — the player who was scored on serves |
| 10 | [diff-9-10.md](diff-9-10.md) | pong-10 — "The Victory Update" | Win condition, `done` state, restart |
| 11 | [diff-10-11.md](diff-10-11.md) | pong-11 — "The Audio Update" | Sound effects, win at 10 points |
| 12 | [diff-11-12.md](diff-11-12.md) | pong-12 — "The Resize Update" | Correct scaling when the window is resized |
| 13 | [diff-12-final.md](diff-12-final.md) | pong-final | Code clean-up, no new features |

## Files per version

| Version | Files |
|---|---|
| pong-0 | `main.lua` |
| pong-1 – pong-4 | + `push.lua`, `font.ttf` (from pong-2) |
| pong-5 – pong-10 | + `class.lua`, `Paddle.lua`, `Ball.lua` |
| pong-11 – pong-final | + `sounds/` folder |
