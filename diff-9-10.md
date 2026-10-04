# Pong: from `pong-9` to `pong-10` — "The Victory Update"

## 1. General task description

In `pong-9` the game has serving and scoring, but it never ends — scores just keep growing.

In `pong-10` we add a **win condition** and a fourth game state, **`done`**:

- when a player reaches the winning score, the game shows **"Player N wins!"** in a medium-size font;
- pressing **Enter** restarts: scores reset to 0 and the **loser** serves first (for fairness).

```
start → serve → play → (point) → serve …
                  └→ (winning point) → done → Enter → serve
```

> In the repo's `pong-10` the winning score is **2**, so you can test the victory screen quickly (the comment in the code says 10). It is changed to 10 in `pong-11`.

We also move the scoring checks **inside** the `play` block, so points can only happen during play.

### Files

| File | What happens |
|---|---|
| `main.lua` | Edited (all the work in this step) |
| `Ball.lua`, `Paddle.lua` | Unchanged |
| `class.lua`, `push.lua`, `font.ttf` | Third-party, unchanged |

## 2. Steps from `pong-9` to `pong-10`

### Step 1. Update the header comment

```lua
    pong-10
    "The Victory Update"
```

### Step 2. Add a medium-size font

In `love.load()`, group the fonts together and add `largeFont` (size 16):

```lua
    smallFont = love.graphics.newFont('font.ttf', 8)
    largeFont = love.graphics.newFont('font.ttf', 16)
    scoreFont = love.graphics.newFont('font.ttf', 32)
    love.graphics.setFont(smallFont)
```

### Step 3. Move the scoring checks inside the `play` block

In `love.update(dt)`, cut the two blocks `if ball.x < 0 … end` and `if ball.x > VIRTUAL_WIDTH … end` and paste them **inside** `elseif gameState == 'play' then`, right after the bottom-wall check. The `end` that closed the play block moves down below them.

### Step 4. Check for a winner — left edge

Rewrite the left-edge block so it checks for victory:

```lua
        if ball.x < 0 then
            servingPlayer = 1
            player2Score = player2Score + 1

            if player2Score == 2 then
                winningPlayer = 2
                gameState = 'done'
            else
                gameState = 'serve'
                ball:reset()
            end
        end
```

> If player 2 just reached the winning score, we go to `done` and remember the winner in a new global `winningPlayer`. Otherwise we continue as before.

### Step 5. Check for a winner — right edge

Same for player 1:

```lua
        if ball.x > VIRTUAL_WIDTH then
            servingPlayer = 2
            player1Score = player1Score + 1

            if player1Score == 2 then
                winningPlayer = 1
                gameState = 'done'
            else
                gameState = 'serve'
                ball:reset()
            end
        end
```

### Step 6. Restart from `done` with Enter

In `love.keypressed`, add a third branch to the Enter logic:

```lua
        elseif gameState == 'done' then
            gameState = 'serve'

            ball:reset()

            player1Score = 0
            player2Score = 0

            -- the player who lost serves first
            if winningPlayer == 1 then
                servingPlayer = 2
            else
                servingPlayer = 1
            end
        end
```

### Step 7. Draw the victory message

In `love.draw()`, add a branch for `done` at the end of the state-message `if` chain:

```lua
    elseif gameState == 'done' then
        love.graphics.setFont(largeFont)
        love.graphics.printf('Player ' .. tostring(winningPlayer) .. ' wins!',
            0, 10, VIRTUAL_WIDTH, 'center')
        love.graphics.setFont(smallFont)
        love.graphics.printf('Press Enter to restart!', 0, 30, VIRTUAL_WIDTH, 'center')
    end
```

### Step 8. Run and check

Run `love .` and play. When one player gets 2 points, the game stops and shows "Player N wins!". Press **Enter** — the scores reset and the loser gets to serve.
