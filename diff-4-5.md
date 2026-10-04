# Pong: from `pong-4` to `pong-5` — "The Class Update"

## 1. General task description

In `pong-4` the paddles and the ball are a pile of separate global variables (`player1Y`, `ballX`, `ballDX`, …) and all their logic lives inside `main.lua`.

In `pong-5` we **refactor** the code using **classes** (object-oriented programming):

- a `Paddle` class keeps a paddle's position, size, speed, and knows how to update and draw itself;
- a `Ball` class does the same for the ball, plus a `reset()` method;
- `main.lua` now just creates objects (`player1`, `player2`, `ball`) and calls their methods.

Lua has no built-in classes, so we include the third-party **hump.class** library. The game looks and plays almost the same as `pong-4` — this step is about organizing the code.

### Files

| File | What happens |
|---|---|
| `class.lua` | **New.** Third-party library (hump.class by Matthias Richter, [github.com/vrld/hump](https://github.com/vrld/hump/blob/master/class.lua), MIT license) — copy it in, don't write it |
| `Paddle.lua` | **New.** Our own code — write it |
| `Ball.lua` | **New.** Our own code — write it |
| `main.lua` | Edited |
| `push.lua` | Third-party library, unchanged |
| `font.ttf` | Third-party font, unchanged |

## 2. Steps from `pong-4` to `pong-5`

### Step 1. Add the class library

Copy `class.lua` into the project folder. It gives us a `Class{}` function for defining classes.

### Step 2. Create `Paddle.lua`

Create a new file `Paddle.lua`:

```lua
Paddle = Class{}

function Paddle:init(x, y, width, height)
    self.x = x
    self.y = y
    self.width = width
    self.height = height
    self.dy = 0
end

function Paddle:update(dt)
    if self.dy < 0 then
        -- moving up: don't go above the top of the screen
        self.y = math.max(0, self.y + self.dy * dt)
    else
        -- moving down: don't go below the bottom of the screen
        self.y = math.min(VIRTUAL_HEIGHT - self.height, self.y + self.dy * dt)
    end
end

function Paddle:render()
    love.graphics.rectangle('fill', self.x, self.y, self.width, self.height)
end
```

> - `init` runs once when a new object is created — like a constructor.
> - `self` is "this particular paddle". Each paddle object has its own `x`, `y`, etc.
> - `Paddle:update(dt)` is shorthand for `Paddle.update(self, dt)`.
> - The paddle now stores its **speed** (`dy`) instead of `main.lua` moving it directly. The screen-edge clamping from `pong-4` moved in here, and it now uses `self.height` instead of a hard-coded 20.

### Step 3. Create `Ball.lua`

Create a new file `Ball.lua`:

```lua
Ball = Class{}

function Ball:init(x, y, width, height)
    self.x = x
    self.y = y
    self.width = width
    self.height = height

    self.dy = math.random(2) == 1 and -100 or 100
    self.dx = math.random(-50, 50)
end

function Ball:reset()
    self.x = VIRTUAL_WIDTH / 2 - 2
    self.y = VIRTUAL_HEIGHT / 2 - 2
    self.dy = math.random(2) == 1 and -100 or 100
    self.dx = math.random(-50, 50)
end

function Ball:update(dt)
    self.x = self.x + self.dx * dt
    self.y = self.y + self.dy * dt
end

function Ball:render()
    love.graphics.rectangle('fill', self.x, self.y, self.width, self.height)
end
```

> Note: compared to `pong-4`, the original code here swaps `dx` and `dy` — the fixed ±100 speed is on the **Y** axis, so the ball now flies mostly up/down. This is how the repo's `pong-5` behaves; a faster horizontal speed is added in `pong-7`.

### Step 4. Update the header comment in `main.lua`

```lua
    pong-5
    "The Class Update"
```

### Step 5. Require the class library and the new classes

In `main.lua`, right after `push = require 'push'`:

```lua
Class = require 'class'

require 'Paddle'
require 'Ball'
```

> `class.lua` returns the `Class` function, so we store it. `Paddle.lua` and `Ball.lua` create global `Paddle` and `Ball` themselves, so a plain `require` is enough. `Class` must be loaded **before** them, because they use it.

### Step 6. Replace the variables with objects in `love.load()`

Delete `player1Y`, `player2Y`, `ballX`, `ballY`, `ballDX`, `ballDY` and write instead:

```lua
    player1 = Paddle(10, 30, 5, 20)
    player2 = Paddle(VIRTUAL_WIDTH - 10, VIRTUAL_HEIGHT - 30, 5, 20)

    ball = Ball(VIRTUAL_WIDTH / 2 - 2, VIRTUAL_HEIGHT / 2 - 2, 4, 4)
```

> Calling `Paddle(...)` creates a new object and runs `Paddle:init(...)`. (Player 2 now starts at `VIRTUAL_HEIGHT - 30`, slightly lower than before.)

### Step 7. Make input set paddle speed, not position

In `love.update(dt)`, rewrite the movement blocks:

```lua
    -- player 1 movement
    if love.keyboard.isDown('w') then
        player1.dy = -PADDLE_SPEED
    elseif love.keyboard.isDown('s') then
        player1.dy = PADDLE_SPEED
    else
        player1.dy = 0
    end

    -- player 2 movement
    if love.keyboard.isDown('up') then
        player2.dy = -PADDLE_SPEED
    elseif love.keyboard.isDown('down') then
        player2.dy = PADDLE_SPEED
    else
        player2.dy = 0
    end
```

> The new `else` branch is important: when no key is pressed, the speed must go back to 0 or the paddle would keep sliding.

### Step 8. Call the objects' `update` methods

Replace the ball movement and add paddle updates at the end of `love.update(dt)`:

```lua
    if gameState == 'play' then
        ball:update(dt)
    end

    player1:update(dt)
    player2:update(dt)
```

### Step 9. Use `ball:reset()` on Enter

In `love.keypressed`, replace the four lines that reset the ball's position and velocity with:

```lua
            ball:reset()
```

### Step 10. Draw with `render()` methods

In `love.draw()`, replace the three `love.graphics.rectangle` lines with:

```lua
    player1:render()
    player2:render()

    ball:render()
```

### Step 11. Run and check

Run `love .`. Everything works as in `pong-4`: Enter starts and resets, paddles move and stay on screen. The ball now moves mostly vertically (see the note in Step 3).
