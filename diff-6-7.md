# Pong: from `pong-6` to `pong-7` — "The Collision Update"

## 1. General task description

In `pong-6` the ball has a `collides` method, but nothing uses it: the ball flies through paddles and off the screen.

In `pong-7` the ball finally **bounces**:

- off the **paddles** — it reverses horizontal direction, speeds up by 3% each hit, and gets a random vertical angle;
- off the **top and bottom walls** — it reverses vertical direction.

We also give the ball a proper fast **horizontal** starting speed, so it travels toward a player instead of mostly up and down.

### Files

| File | What happens |
|---|---|
| `main.lua` | Edited |
| `Ball.lua` | Edited — new starting `dx` |
| `Paddle.lua` | Unchanged |
| `class.lua`, `push.lua`, `font.ttf` | Third-party, unchanged |

## 2. Steps from `pong-6` to `pong-7`

### Step 1. Update the header comment

```lua
    pong-7
    "The Collision Update"
```

### Step 2. Give the ball a faster horizontal start

In `Ball.lua`, inside `Ball:init`, replace the `self.dx` line:

```lua
    self.dx = math.random(2) == 1 and math.random(-80, -100) or math.random(80, 100)
```

> 50% chance to go left at about 80–100 px/s, 50% to go right. (In standard Lua you'd write `math.random(-100, -80)` — smaller number first. LÖVE's LuaJIT accepts the order used in the repo.)
>
> Only `init` changes here — `Ball:reset()` still uses the old `math.random(-50, 50)`.

### Step 3. Start a collision block in `love.update(dt)`

At the **very beginning** of `love.update(dt)`, before the paddle input code, open a block that only runs during play:

```lua
    if gameState == 'play' then
        -- (Steps 4–6 go here)
    end
```

### Step 4. Bounce off player 1 (left paddle)

Inside that block:

```lua
        if ball:collides(player1) then
            ball.dx = -ball.dx * 1.03
            ball.x = player1.x + 5

            if ball.dy < 0 then
                ball.dy = -math.random(10, 150)
            else
                ball.dy = math.random(10, 150)
            end
        end
```

> - `-ball.dx` reverses the direction; `* 1.03` makes it 3% faster each hit, so rallies get harder.
> - `ball.x = player1.x + 5` pushes the ball just outside the paddle (paddle width is 5). Without this, the ball could still overlap next frame and "collide" again, getting stuck.
> - The vertical speed is randomized but keeps the same up/down direction.

### Step 5. Bounce off player 2 (right paddle)

Same idea, but the ball is pushed to the **left** of the paddle (ball width is 4):

```lua
        if ball:collides(player2) then
            ball.dx = -ball.dx * 1.03
            ball.x = player2.x - 4

            if ball.dy < 0 then
                ball.dy = -math.random(10, 150)
            else
                ball.dy = math.random(10, 150)
            end
        end
```

### Step 6. Bounce off the top and bottom walls

Still inside the `if gameState == 'play'` block:

```lua
        -- top wall
        if ball.y <= 0 then
            ball.y = 0
            ball.dy = -ball.dy
        end

        -- bottom wall (-4 for the ball's height)
        if ball.y >= VIRTUAL_HEIGHT - 4 then
            ball.y = VIRTUAL_HEIGHT - 4
            ball.dy = -ball.dy
        end
```

### Step 7. (Optional) FPS color in 0–255 notation

In `displayFPS()`, the repo rewrites green as:

```lua
    love.graphics.setColor(0, 255/255, 0, 255/255)
```

This is exactly the same color as `(0, 1, 0, 1)` — just written the "0–255 divided by 255" way, matching the background color style.

### Step 8. Run and check

Run `love .` and press **Enter**. The ball heads left or right, bounces off the top and bottom walls, and bounces off paddles, speeding up every hit. If it gets past a paddle it still flies off the screen — scoring comes in the next step.
