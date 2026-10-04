# Pong: from `pong-5` to `pong-6` — "The FPS Update"

## 1. General task description

`pong-5` reorganized the code into classes but hid the score.

In `pong-6` we add small but useful things:

- the window gets a **title** ("Pong");
- the **score display** comes back (both scores still stay 0);
- an **FPS counter** (frames per second) is drawn in green in the top-left corner — handy for checking performance;
- the `Ball` class gets a **`collides(paddle)`** method that checks whether the ball overlaps a paddle. It isn't used yet — that happens in `pong-7`.

### Files

| File | What happens |
|---|---|
| `main.lua` | Edited |
| `Ball.lua` | Edited — new `collides` method |
| `Paddle.lua` | Unchanged |
| `class.lua`, `push.lua`, `font.ttf` | Third-party, unchanged |

## 2. Steps from `pong-5` to `pong-6`

### Step 1. Update the header comment

```lua
    pong-6
    "The FPS Update"
```

### Step 2. Set the window title

In `love.load()`, after `setDefaultFilter`:

```lua
    love.window.setTitle('Pong')
```

### Step 3. Bring back the score font

In `love.load()`, after `smallFont`:

```lua
    scoreFont = love.graphics.newFont('font.ttf', 32)
```

### Step 4. Bring back the score variables

In `love.load()`, after `push.setupScreen(...)` and before creating the paddles:

```lua
    player1Score = 0
    player2Score = 0
```

### Step 5. Draw the scores

In `love.draw()`, after the "Hello … State!" text and before rendering the paddles:

```lua
    love.graphics.setFont(scoreFont)
    love.graphics.print(tostring(player1Score), VIRTUAL_WIDTH / 2 - 50,
        VIRTUAL_HEIGHT / 3)
    love.graphics.print(tostring(player2Score), VIRTUAL_WIDTH / 2 + 30,
        VIRTUAL_HEIGHT / 3)
```

### Step 6. Write a `displayFPS` function

At the very end of `main.lua`, add a new function:

```lua
function displayFPS()
    love.graphics.setFont(smallFont)
    love.graphics.setColor(0, 1, 0, 1)
    love.graphics.print('FPS: ' .. tostring(love.timer.getFPS()), 10, 10)
    love.graphics.setColor(1, 1, 1, 1)
end
```

> - `love.timer.getFPS()` returns the current frames per second.
> - `..` joins (concatenates) strings in Lua.
> - `setColor(0, 1, 0, 1)` = green (red, green, blue, alpha; values 0–1). We set the color back to white afterwards, otherwise **everything drawn after it** would also be green.

### Step 7. Call `displayFPS` in `love.draw()`

Right after `ball:render()` and before `push.finish()`:

```lua
    displayFPS()
```

### Step 8. Add collision detection to `Ball.lua`

In `Ball.lua`, between `Ball:init` and `Ball:reset`, add:

```lua
function Ball:collides(paddle)
    -- is one rectangle completely to the left/right of the other?
    if self.x >= paddle.x + paddle.width or paddle.x >= self.x + self.width then
        return false
    end

    -- is one rectangle completely above/below the other?
    if self.y >= paddle.y + paddle.height or paddle.y >= self.y + self.height then
        return false
    end

    -- otherwise they overlap
    return true
end
```

> This is **AABB collision** (axis-aligned bounding boxes). Two rectangles do *not* touch if one is fully to the side of the other, or fully above/below it. If neither is true, they overlap.

### Step 9. Run and check

Run `love .`. The window title is "Pong", both scores show 0, and a green "FPS: 60" (or similar) appears in the top-left corner. The ball still passes through paddles — the collision check is wired up in the next step.
