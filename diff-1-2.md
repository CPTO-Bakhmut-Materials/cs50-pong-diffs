# Pong: from `pong-1` to `pong-2` — "The Rectangle Update"

## 1. General task description

In `pong-1` the game only shows the text **"Hello Pong!"** in the middle of a low-resolution (virtual 432×243) screen.

In `pong-2` we start drawing the actual game objects. The goal of this step is to make the screen *look* like Pong, even though nothing moves yet:

- use a retro pixel font instead of LÖVE's default font;
- paint the background a dark gray-blue color, like some versions of the original Pong;
- move the welcome text to the top of the screen;
- draw two paddles (left and right) and a ball as simple white rectangles.

### Files

| File | What happens |
|---|---|
| `main.lua` | Edited (all the work in this step) |
| `push.lua` | Third-party library, unchanged — keep it from `pong-1` |
| `font.ttf` | **New.** Third-party font file — just copy it into the project folder, don't create it |

## 2. Steps from `pong-1` to `pong-2`

### Step 1. Add the font file

Copy `font.ttf` from the repo into your project folder, next to `main.lua` and `push.lua`. It is a ready-made pixel font; we only include it.

### Step 2. Update the header comment

At the top of `main.lua`, change the version name:

```lua
    pong-2
    "The Rectangle Update"
```

### Step 3. Load the retro font in `love.load()`

Right after `love.graphics.setDefaultFilter('nearest', 'nearest')`, create a font object of size 8 and make it the active font:

```lua
    -- more "retro-looking" font object we can use for any text
    smallFont = love.graphics.newFont('font.ttf', 8)

    -- set LÖVE2D's active font to the smallFont object
    love.graphics.setFont(smallFont)
```

> Why size 8? We draw at a tiny virtual resolution, so a small font scales up into crisp, chunky pixels. The `'nearest'` filter keeps it sharp.

### Step 4. Clear the screen with a background color

In `love.draw()`, right after `push.start()`, fill the whole screen with a dark color:

```lua
    love.graphics.clear(40/255, 45/255, 52/255, 255/255)
```

> LÖVE 11+ expects colors in the range 0–1, so we divide the familiar 0–255 values by 255.

### Step 5. Move the welcome text to the top

Replace the old centered `printf` with one that puts the text 20 pixels from the top:

```lua
    love.graphics.printf('Hello Pong!', 0, 20, VIRTUAL_WIDTH, 'center')
```

### Step 6. Draw the left paddle

Paddles are just filled rectangles 5 px wide and 20 px tall:

```lua
    -- render first paddle (left side)
    love.graphics.rectangle('fill', 10, 30, 5, 20)
```

Arguments: `mode, x, y, width, height`.

### Step 7. Draw the right paddle

Place it near the right edge and near the bottom:

```lua
    -- render second paddle (right side)
    love.graphics.rectangle('fill', VIRTUAL_WIDTH - 10, VIRTUAL_HEIGHT - 50, 5, 20)
```

### Step 8. Draw the ball

A 4×4 square in the center. We subtract half its size (2) so the ball's *center*, not its top-left corner, is in the middle of the screen:

```lua
    -- render ball (center)
    love.graphics.rectangle('fill', VIRTUAL_WIDTH / 2 - 2, VIRTUAL_HEIGHT / 2 - 2, 4, 4)
```

All drawing must stay between `push.start()` and `push.finish()`.

### Step 9. Run and check

Run the project (`love .`). You should see a dark background, "Hello Pong!" at the top in a pixel font, a paddle on the upper left, a paddle on the lower right, and a small ball in the center. Nothing moves yet — that comes in the next step.

## Final `love.draw()` for reference

```lua
function love.draw()
    push.start()

    love.graphics.clear(40/255, 45/255, 52/255, 255/255)

    love.graphics.printf('Hello Pong!', 0, 20, VIRTUAL_WIDTH, 'center')

    love.graphics.rectangle('fill', 10, 30, 5, 20)
    love.graphics.rectangle('fill', VIRTUAL_WIDTH - 10, VIRTUAL_HEIGHT - 50, 5, 20)
    love.graphics.rectangle('fill', VIRTUAL_WIDTH / 2 - 2, VIRTUAL_HEIGHT / 2 - 2, 4, 4)

    push.finish()
end
```
