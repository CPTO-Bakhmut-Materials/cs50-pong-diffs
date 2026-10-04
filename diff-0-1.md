# Pong: from `pong-0` to `pong-1` — "The Low-Res Update"

## 1. General task description

In `pong-0` the game opens a 1280×720 window and prints **"Hello Pong!"** in LÖVE's default font, at full window resolution.

In `pong-1` we give the game its retro look by drawing everything at a much smaller **virtual resolution** (432×243) and scaling it up to the window. To do this we include the third-party library **push**. We also:

- turn on nearest-neighbor filtering so the upscaled pixels stay sharp instead of blurry;
- make the window resizable (push keeps the picture scaled correctly);
- let the player quit the game with the **Escape** key.

### Files

| File | What happens |
|---|---|
| `main.lua` | Edited |
| `push.lua` | **New.** Third-party library ([github.com/Ulydev/push](https://github.com/Ulydev/push), MIT license) — download/copy it into the project folder, don't write it |

## 2. Steps from `pong-0` to `pong-1`

### Step 1. Add the push library

Copy `push.lua` into your project folder, next to `main.lua`. It handles rendering at a virtual resolution and scaling it to the real window.

### Step 2. Update the header comment

```lua
    pong-1
    "The Low-Res Update"
```

### Step 3. Require push

At the top of `main.lua`, before the window constants:

```lua
push = require 'push'
```

> `require 'push'` loads `push.lua` from the same folder (no `.lua` extension needed).

### Step 4. Add virtual resolution constants

Below `WINDOW_WIDTH` and `WINDOW_HEIGHT`:

```lua
VIRTUAL_WIDTH = 432
VIRTUAL_HEIGHT = 243
```

> 432×243 keeps the same 16:9 ratio as 1280×720, close to the resolution of old consoles like the NES.

### Step 5. Turn on nearest-neighbor filtering

First line inside `love.load()`:

```lua
    love.graphics.setDefaultFilter('nearest', 'nearest')
```

> Without this, LÖVE smooths (blurs) graphics when scaling them. Try removing it later to see the difference.

### Step 6. Make the window resizable

In the `love.window.setMode` options, change `resizable = false` to:

```lua
        resizable = true,
```

### Step 7. Set up push

At the end of `love.load()`:

```lua
    push.setupScreen(VIRTUAL_WIDTH, VIRTUAL_HEIGHT, { upscale = 'normal' })
```

### Step 8. Quit with Escape

Add a new function after `love.load()`. LÖVE calls it every time a key is pressed:

```lua
function love.keypressed(key)
    if key == 'escape' then
        love.event.quit()
    end
end
```

### Step 9. Draw through push

Rewrite `love.draw()` so everything is drawn between `push.start()` and `push.finish()`, and position the text using the **virtual** size instead of the window size:

```lua
function love.draw()
    push.start()

    love.graphics.printf('Hello Pong!', 0, VIRTUAL_HEIGHT / 2 - 6, VIRTUAL_WIDTH, 'center')

    push.finish()
end
```

> From now on all coordinates in the game are in virtual pixels (0–432 horizontally, 0–243 vertically).

### Step 10. Run and check

Run `love .`. The text is now large and pixelated — the same default font, but drawn small and scaled up. Resize the window: the picture rescales. Press **Escape** to quit.
