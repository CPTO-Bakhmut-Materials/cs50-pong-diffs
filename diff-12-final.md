# Pong: from `pong-12` to `pong-final` — Clean-up

## 1. General task description

`pong-12` is already a complete, playable game. `pong-final` adds **no new features** — it **cleans up** the code:

- the `Ball` class no longer gives itself a random velocity. Since `pong-9` the **serve** state sets the ball's speed, so `init` and `reset` now just place the ball in the center, **standing still**. This removes leftover, inconsistent random code from earlier versions;
- `love.load()` is reordered so related things are together, and all variables are initialized explicitly (`winningPlayer = 0`);
- the score is drawn **after** the state messages, so the ball and paddles are drawn on top of it;
- comments are rewritten to explain each LÖVE callback, and the version name is removed from the header.

### Files

| File | What happens |
|---|---|
| `Ball.lua` | Edited — no random velocity in `init` / `reset` |
| `main.lua` | Edited — reordering and comments |
| `Paddle.lua` | Unchanged |
| `sounds/*.wav` | Ready-made assets, unchanged |
| `class.lua`, `push.lua`, `font.ttf` | Third-party, unchanged |

## 2. Steps from `pong-12` to `pong-final`

### Step 1. Make the ball start still in `Ball:init`

In `Ball.lua`, replace the two random velocity lines in `Ball:init` with:

```lua
    self.dy = 0
    self.dx = 0
```

### Step 2. Make `Ball:reset` stop the ball

Replace the body of `Ball:reset`:

```lua
function Ball:reset()
    self.x = VIRTUAL_WIDTH / 2 - 2
    self.y = VIRTUAL_HEIGHT / 2 - 2
    self.dx = 0
    self.dy = 0
end
```

> The ball only gets a speed in the `serve` state in `main.lua`. Keeping velocity logic in **one** place makes the code easier to understand and avoids bugs.

### Step 3. Remove the version name from the header

In `main.lua`, delete these two lines from the top comment:

```lua
    pong-12
    "The Resize Update"
```

### Step 4. Reorder object and variable setup in `love.load()`

After `push.setupScreen(...)`, put things in this order:

```lua
    -- paddles and ball
    player1 = Paddle(10, 30, 5, 20)
    player2 = Paddle(VIRTUAL_WIDTH - 10, VIRTUAL_HEIGHT - 30, 5, 20)
    ball = Ball(VIRTUAL_WIDTH / 2 - 2, VIRTUAL_HEIGHT / 2 - 2, 4, 4)

    -- scores
    player1Score = 0
    player2Score = 0

    -- whoever is scored on serves next
    servingPlayer = 1

    -- not set to a real value until someone wins
    winningPlayer = 0

    -- 'start', 'serve', 'play' or 'done'
    gameState = 'start'
```

> `winningPlayer = 0` is new. The game worked without it (it was created later, when someone won), but declaring every variable up front makes the program easier to read.

### Step 5. Draw the score after the state messages

In `love.draw()`:

1. Remove the `love.graphics.setFont(smallFont)` and `displayScore()` lines that come right after `love.graphics.clear(...)`.
2. Add `displayScore()` **after** the whole `if gameState == …` message block and **before** `player1:render()`:

```lua
    -- show the score before the ball is rendered so it can move over the text
    displayScore()

    player1:render()
    player2:render()
    ball:render()
```

> Things drawn later appear on top. Now the ball passes **over** the big score digits instead of under them.

### Step 6. Move `displayFPS` to the end of the file

Move `displayFPS()` below `displayScore()` (order of function definitions doesn't change behavior — it's just tidier). In it, the repo resets the color with:

```lua
    love.graphics.setColor(255, 255, 255, 255)
```

> In LÖVE 11+ this is the same as `(1, 1, 1, 1)` (values above 1 are treated as 1), so either works.

### Step 7. Improve comments (optional)

Rewrite the comments above `love.load`, `love.resize`, `love.update`, `love.keypressed` and `love.draw` so they explain *when* LÖVE calls each one — for example, that `love.keypressed` fires once per key press, while `love.keyboard.isDown` is for held keys.

### Step 8. Run and check

Run `love .`. The game plays exactly like `pong-12`. The difference is in the code: the ball's velocity is handled in one place, setup is grouped logically, and the ball draws over the score.
