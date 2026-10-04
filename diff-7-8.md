# Pong: from `pong-7` to `pong-8` — "The Score Update"

## 1. General task description

In `pong-7` the ball bounces, but when a player misses, the ball just flies off the screen forever.

In `pong-8` we add **scoring**:

- if the ball leaves the **left** edge, player 2 scores;
- if it leaves the **right** edge, player 1 scores;
- after a point the ball resets to the center and the game returns to the `'start'` state (press Enter to continue).

We also record who should serve next (`servingPlayer`) — it isn't used yet, but prepares the next step. The "Hello Start/Play State!" debug text is removed.

### Files

| File | What happens |
|---|---|
| `main.lua` | Edited (all the work in this step) |
| `Ball.lua`, `Paddle.lua` | Unchanged |
| `class.lua`, `push.lua`, `font.ttf` | Third-party, unchanged |

## 2. Steps from `pong-7` to `pong-8`

### Step 1. Update the header comment

```lua
    pong-8
    "The Score Update"
```

### Step 2. Detect the ball leaving the left edge

In `love.update(dt)`, **after** the `if gameState == 'play' then … end` collision block and **before** the paddle input code:

```lua
    if ball.x < 0 then
        servingPlayer = 1
        player2Score = player2Score + 1
        ball:reset()
        gameState = 'start'
    end
```

> Player 1 missed, so player 2 gets the point and player 1 (the one who lost the point) will serve next.

### Step 3. Detect the ball leaving the right edge

Right below:

```lua
    if ball.x > VIRTUAL_WIDTH then
        servingPlayer = 2
        player1Score = player1Score + 1
        ball:reset()
        gameState = 'start'
    end
```

> `servingPlayer` is a new global variable. It's only set here for now; `pong-9` will use it.

### Step 4. Remove the debug state text

In `love.draw()`, delete the whole block:

```lua
    if gameState == 'start' then
        love.graphics.printf('Hello Start State!', 0, 20, VIRTUAL_WIDTH, 'center')
    else
        love.graphics.printf('Hello Play State!', 0, 20, VIRTUAL_WIDTH, 'center')
    end
```

(Keep the `love.graphics.setFont(smallFont)` line above it.)

### Step 5. Run and check

Run `love .` and press **Enter**. Let the ball past one paddle: the opponent's score goes up by 1, the ball returns to the center, and the game waits. Press **Enter** to serve again.
