# Pong: from `pong-8` to `pong-9` — "The Serve Update"

## 1. General task description

In `pong-8` points are scored, but after each point the game jumps back to `'start'` and the ball goes in a random direction — no one really "serves".

In `pong-9` we add a proper **serve** phase. The game now has three states:

```
start  --Enter-->  serve  --Enter-->  play
                     ^                  |
                     +---- point -------+
```

- **start** — "Welcome to Pong! Press Enter to begin!"
- **serve** — "Player N's serve! Press Enter to serve!" The ball will fly **away from** the serving player, toward the opponent.
- **play** — no messages, the ball is in motion.

The player who **was scored on** serves next. The score drawing is also moved into its own `displayScore()` function.

### Files

| File | What happens |
|---|---|
| `main.lua` | Edited (all the work in this step) |
| `Ball.lua`, `Paddle.lua` | Unchanged |
| `class.lua`, `push.lua`, `font.ttf` | Third-party, unchanged |

## 2. Steps from `pong-8` to `pong-9`

### Step 1. Update the header comment

```lua
    pong-9
    "The Serve Update"
```

### Step 2. Initialize the serving player

In `love.load()`, after the score variables:

```lua
    -- 1 or 2; whoever is scored on serves the next turn
    servingPlayer = 1
```

### Step 3. Prepare the ball's direction in the `serve` state

At the start of `love.update(dt)`, change `if gameState == 'play' then` into an `if / elseif`:

```lua
    if gameState == 'serve' then
        ball.dy = math.random(-50, 50)
        if servingPlayer == 1 then
            ball.dx = math.random(140, 200)
        else
            ball.dx = -math.random(140, 200)
        end
    elseif gameState == 'play' then
        -- (the existing paddle and wall collision code stays here)
    end
```

> Player 1 is on the left, so their serve goes **right** (positive `dx`). Player 2's serve goes **left**. The serve is also faster than before (140–200 px/s).

### Step 4. Go to `serve` after a point

In the two "ball left the screen" blocks, change `gameState = 'start'` to:

```lua
        gameState = 'serve'
```

(in both the `ball.x < 0` and `ball.x > VIRTUAL_WIDTH` blocks).

### Step 5. Rewrite the Enter key logic

In `love.keypressed`, replace the Enter branch with:

```lua
    elseif key == 'enter' or key == 'return' then
        if gameState == 'start' then
            gameState = 'serve'
        elseif gameState == 'serve' then
            gameState = 'play'
        end
    end
```

> Enter no longer resets the ball during play — the old `ball:reset()` call and `else` branch are removed. Resetting happens only after a point.

### Step 6. Move score drawing into `displayScore()`

At the end of `main.lua` add:

```lua
function displayScore()
    love.graphics.setFont(scoreFont)
    love.graphics.print(tostring(player1Score), VIRTUAL_WIDTH / 2 - 50,
        VIRTUAL_HEIGHT / 3)
    love.graphics.print(tostring(player2Score), VIRTUAL_WIDTH / 2 + 30,
        VIRTUAL_HEIGHT / 3)
end
```

In `love.draw()`, delete the old score-drawing lines and put this call in their place:

```lua
    displayScore()
```

### Step 7. Show messages for each state

In `love.draw()`, right after `displayScore()`:

```lua
    if gameState == 'start' then
        love.graphics.setFont(smallFont)
        love.graphics.printf('Welcome to Pong!', 0, 10, VIRTUAL_WIDTH, 'center')
        love.graphics.printf('Press Enter to begin!', 0, 20, VIRTUAL_WIDTH, 'center')
    elseif gameState == 'serve' then
        love.graphics.setFont(smallFont)
        love.graphics.printf('Player ' .. tostring(servingPlayer) .. "'s serve!",
            0, 10, VIRTUAL_WIDTH, 'center')
        love.graphics.printf('Press Enter to serve!', 0, 20, VIRTUAL_WIDTH, 'center')
    elseif gameState == 'play' then
        -- no UI messages to display in play
    end
```

> We set `smallFont` again because `displayScore()` just switched to `scoreFont`. The string `"'s serve!"` uses double quotes so it can contain the apostrophe.

### Step 8. (Optional) FPS color

The repo changes the FPS color to `love.graphics.setColor(0, 255, 0, 255)`. In LÖVE 11+ values above 1 are treated as 1, so it's still the same green — you can keep `(0, 1, 0, 1)`.

### Step 9. Run and check

Run `love .`. You see "Welcome to Pong!". **Enter** → "Player 1's serve!". **Enter** → the ball flies right. When someone misses, the score updates and the player who was scored on is shown as the next server.
