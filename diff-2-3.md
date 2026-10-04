# Pong: from `pong-2` to `pong-3` — "The Paddle Update"

## 1. General task description

In `pong-2` the paddles and ball are drawn, but nothing moves.

In `pong-3` the players can **move their paddles up and down**:

- Player 1 (left) uses **W** / **S**.
- Player 2 (right) uses **↑** / **↓**.

Movement is done in `love.update(dt)` and multiplied by `dt` (delta time) so the speed is the same on fast and slow computers. We also add score variables and draw the scores in a big font in the middle of the screen (they always stay 0 for now).

> Note: in this version paddles can still move off the screen. That gets fixed later.

### Files

| File | What happens |
|---|---|
| `main.lua` | Edited (all the work in this step) |
| `push.lua` | Third-party library, unchanged |
| `font.ttf` | Third-party font, unchanged |

## 2. Steps from `pong-2` to `pong-3`

### Step 1. Update the header comment

```lua
    pong-3
    "The Paddle Update"
```

### Step 2. Add a paddle speed constant

Below `VIRTUAL_WIDTH` / `VIRTUAL_HEIGHT`:

```lua
-- speed at which we will move our paddle; multiplied by dt in update
PADDLE_SPEED = 200
```

> 200 means "200 virtual pixels per second".

### Step 3. Create a larger font for the score

In `love.load()`, right after `smallFont` is created:

```lua
    scoreFont = love.graphics.newFont('font.ttf', 32)
```

### Step 4. Initialize scores and paddle positions

At the end of `love.load()`:

```lua
    player1Score = 0
    player2Score = 0

    -- paddle positions on the Y axis (they can only move up or down)
    player1Y = 30
    player2Y = VIRTUAL_HEIGHT - 50
```

> These are the same Y values we hard-coded in `pong-2`; now they live in variables so they can change.

### Step 5. Add `love.update(dt)`

Add a new function after `love.load()`. LÖVE calls it every frame and passes `dt` — the time in seconds since the last frame.

```lua
function love.update(dt)
    -- player 1 movement
    if love.keyboard.isDown('w') then
        player1Y = player1Y + -PADDLE_SPEED * dt
    elseif love.keyboard.isDown('s') then
        player1Y = player1Y + PADDLE_SPEED * dt
    end

    -- player 2 movement
    if love.keyboard.isDown('up') then
        player2Y = player2Y + -PADDLE_SPEED * dt
    elseif love.keyboard.isDown('down') then
        player2Y = player2Y + PADDLE_SPEED * dt
    end
end
```

> `love.keyboard.isDown` is true for as long as the key is held — unlike `love.keypressed`, which fires only once per press. In LÖVE, Y grows downward, so "up" means subtracting from Y.

### Step 6. Set the small font before the welcome text

We'll switch fonts in `love.draw()`, so set the small font explicitly before printing "Hello Pong!":

```lua
    love.graphics.setFont(smallFont)
    love.graphics.printf('Hello Pong!', 0, 20, VIRTUAL_WIDTH, 'center')
```

### Step 7. Draw the scores

Right after the welcome text, switch to the big font and print both scores near the center:

```lua
    love.graphics.setFont(scoreFont)
    love.graphics.print(tostring(player1Score), VIRTUAL_WIDTH / 2 - 50,
        VIRTUAL_HEIGHT / 3)
    love.graphics.print(tostring(player2Score), VIRTUAL_WIDTH / 2 + 30,
        VIRTUAL_HEIGHT / 3)
```

> `tostring` converts the number to text for printing.

### Step 8. Draw the paddles at their variable positions

Replace the hard-coded Y values with the new variables:

```lua
    love.graphics.rectangle('fill', 10, player1Y, 5, 20)
    love.graphics.rectangle('fill', VIRTUAL_WIDTH - 10, player2Y, 5, 20)
```

The ball line stays unchanged.

### Step 9. Run and check

Run `love .`. You should see "0" and "0" in large digits. Hold **W/S** and **↑/↓** — the paddles move smoothly. Notice they can leave the screen; we'll fix that later.
