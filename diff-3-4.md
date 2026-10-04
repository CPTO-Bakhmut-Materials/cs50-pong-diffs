# Pong: from `pong-3` to `pong-4` — "The Ball Update"

## 1. General task description

In `pong-3` the paddles move, but the ball just sits in the center, and paddles can leave the screen.

In `pong-4`:

- the **ball moves** in a random direction once the game is started;
- we introduce a **game state** (`'start'` or `'play'`), switched with **Enter**;
- pressing Enter during play **resets** the ball to the center with a new random direction;
- paddles are **clamped** so they can't leave the screen.

To keep this step focused on the ball, the score display from `pong-3` is **removed** for now (the score font and score variables come back in a later version).

> Note: the ball doesn't bounce off anything yet — it flies off the screen. Press Enter twice to reset it.

### Files

| File | What happens |
|---|---|
| `main.lua` | Edited (all the work in this step) |
| `push.lua` | Third-party library, unchanged |
| `font.ttf` | Third-party font, unchanged |

## 2. Steps from `pong-3` to `pong-4`

### Step 1. Update the header comment

```lua
    pong-4
    "The Ball Update"
```

### Step 2. Seed the random number generator

In `love.load()`, right after `setDefaultFilter`:

```lua
    math.randomseed(os.time())
```

> Without a seed, `math.random` gives the same "random" sequence every run. Seeding with the current time makes each run different.

### Step 3. Remove the score for now

In `love.load()` delete:
- the `scoreFont = ...` line;
- the `player1Score = 0` and `player2Score = 0` lines.

In `love.draw()` delete the block that sets `scoreFont` and prints the two scores.

### Step 4. Add ball position and velocity variables

At the end of `love.load()`, after the paddle positions:

```lua
    -- ball starts in the center
    ballX = VIRTUAL_WIDTH / 2 - 2
    ballY = VIRTUAL_HEIGHT / 2 - 2

    -- random starting velocity
    ballDX = math.random(2) == 1 and 100 or -100
    ballDY = math.random(-50, 50)
```

> `DX` / `DY` = change in X / Y per second (velocity).
> `math.random(2) == 1 and 100 or -100` is Lua's version of a ternary operator: 50% chance of going right (100), 50% left (−100).
> `math.random(-50, 50)` gives a random integer between −50 and 50, so the ball goes up or down at a random angle.

### Step 5. Add the game state

Still at the end of `love.load()`:

```lua
    gameState = 'start'
```

> A game state is a simple variable that tells the rest of the code what "mode" the game is in. We'll check it in `update` and `draw`.

### Step 6. Clamp paddle movement to the screen

In `love.update(dt)` wrap each paddle calculation in `math.max` (top edge) or `math.min` (bottom edge):

```lua
    -- player 1 movement
    if love.keyboard.isDown('w') then
        player1Y = math.max(0, player1Y + -PADDLE_SPEED * dt)
    elseif love.keyboard.isDown('s') then
        player1Y = math.min(VIRTUAL_HEIGHT - 20, player1Y + PADDLE_SPEED * dt)
    end

    -- player 2 movement
    if love.keyboard.isDown('up') then
        player2Y = math.max(0, player2Y + -PADDLE_SPEED * dt)
    elseif love.keyboard.isDown('down') then
        player2Y = math.min(VIRTUAL_HEIGHT - 20, player2Y + PADDLE_SPEED * dt)
    end
```

> `math.max(0, y)` never lets Y go below 0 (top). `math.min(VIRTUAL_HEIGHT - 20, y)` never lets the paddle's top go lower than "screen height minus paddle height (20)".

### Step 7. Move the ball during play

At the end of `love.update(dt)`:

```lua
    if gameState == 'play' then
        ballX = ballX + ballDX * dt
        ballY = ballY + ballDY * dt
    end
```

### Step 8. Switch states with Enter

In `love.keypressed(key)`, add an `elseif` branch after the Escape check:

```lua
    elseif key == 'enter' or key == 'return' then
        if gameState == 'start' then
            gameState = 'play'
        else
            gameState = 'start'

            -- reset ball to the center
            ballX = VIRTUAL_WIDTH / 2 - 2
            ballY = VIRTUAL_HEIGHT / 2 - 2

            -- new random velocity
            ballDX = math.random(2) == 1 and 100 or -100
            ballDY = math.random(-50, 50) * 1.5
        end
    end
```

> We check both `'enter'` and `'return'` because the main Enter key is called `'return'` in LÖVE, while `'enter'` covers some other keyboards.

### Step 9. Show the current state on screen

In `love.draw()`, replace the "Hello Pong!" line with:

```lua
    love.graphics.setFont(smallFont)

    if gameState == 'start' then
        love.graphics.printf('Hello Start State!', 0, 20, VIRTUAL_WIDTH, 'center')
    else
        love.graphics.printf('Hello Play State!', 0, 20, VIRTUAL_WIDTH, 'center')
    end
```

### Step 10. Draw the ball at its position

Replace the hard-coded ball rectangle with:

```lua
    love.graphics.rectangle('fill', ballX, ballY, 4, 4)
```

### Step 11. Run and check

Run `love .`. The screen says "Hello Start State!". Press **Enter** — the text changes to "Hello Play State!" and the ball flies off in a random direction. Press **Enter** again to reset it. Paddles now stop at the top and bottom edges.
