---
title: "Playdate Circle Transition"
---

Today in [Playdate](https://play.date) development, I wanted a decidedly old school transition between levels. You know that cheesy effect in silent film where the frame goes black via a shrinking circle? That's an [iris shot](https://en.wikipedia.org/wiki/Iris_shot) my friend. That's what I want.[^1]

<video autoplay loop src="/videos/cabinet-of-dr-caligari-iris-shot.mp4"></video>

This effect requires three things:

1. A black overlay image that covers the entire display.
1. A mask image that cuts a circle out of the overlay.
1. A timer to animate the size of the circle mask.

First let's set up a bare-bones game that switches scenes when you hit the right button.

```lua
import "CoreLibs/graphics"

local gfx <const> = playdate.graphics

local scene1 = gfx.image.new("scene1")
local scene2 = gfx.image.new("scene2")
local currentScene = scene1

local function switchScene()
    if currentScene == scene1 then
        currentScene = scene2
    else
        currentScene = scene1
    end
end

function playdate.update()
    currentScene:draw(0, 0)
end

function playdate.rightButtonDown()
    switchScene()
end
```

<video autoplay height="396" loop src="/videos/playdate-circle-transition-1.mp4" width="660"></video>

Now for that overlay and mask.

```lua
...

local displayWidth, displayHeight = playdate.display.getSize()
local overlay = gfx.image.new(displayWidth, displayHeight, gfx.kColorBlack)
local mask = gfx.image.new(displayWidth, displayHeight)

local function drawCircleCutout(point, radius)
    gfx.pushContext(mask)
        gfx.clear(gfx.kColorWhite)
        gfx.setColor(gfx.kColorBlack)
        gfx.fillCircleAtPoint(point, radius)
    gfx.popContext()

    overlay:setMaskImage(mask)
    overlay:draw(0, 0)
end

...

function playdate.update()
   currentScene:draw(0, 0)
   drawCircleCutout(playdate.geometry.point.new(260, 90), 100)
end

...
```

The interesting API here is `playdate.graphics.pushContext`. This allows us to draw to an image instead of directly to the screen. We take that mask image --- a solid black circle against a white background --- and apply it to the overlay, which we *do* draw to the screen. Black pixels become transparent, white ones stay opaque.

<video autoplay height="396" loop src="/videos/playdate-circle-transition-2.mp4" width="660"></video>

Let's animate this. We could use `playdate.graphics.animator` to animate the circle radius, but I like that `playdate.timer` allows us to set an update callback and call `drawCircleCutout` from there.

```lua
import "CoreLibs/graphics"
import "CoreLibs/timer"

local geo <const> = playdate.geometry
local gfx <const> = playdate.graphics

local transitionDuration = 3000
local transitionPoint = geo.point.new(260, 90)

...

function playdate.update()
    currentScene:draw(0, 0)
    playdate.timer.updateTimers()
end

function playdate.rightButtonDown()
    local maxRadius = 400

    local timer = playdate.timer.new(
        transitionDuration,
        -maxRadius,
        maxRadius)

    timer.updateCallback = function()
        local radius = math.abs(timer.value)
        drawCircleCutout(transitionPoint, radius)
    end

    -- switch scene at transition midpoint when screen is fully black
    playdate.timer.performAfterDelay(transitionDuration / 2, switchScene)
end
```

The timer's value changes from -400 to 400 over the duration of the transition. A negative radius makes no sense, so `abs` makes it positive. The result is a circle radius that bounces from 400 to 0 then back again.

<video autoplay height="396" loop src="/videos/playdate-circle-transition-3.mp4" width="660"></video>

A start and end radius of 400 ensures that the circle is outside the bounds of the screen when the transition starts, but in most cases this is unnecessarily large and results in several frames of animation that appear to do nothing. Let's instead calculate the exact radius we need.

```lua
...

local function getDistanceToFarthestCorner(point)
    return math.max(
        point:distanceToPoint(geo.point.new(0, 0)),
        point:distanceToPoint(geo.point.new(displayWidth, 0)),
        point:distanceToPoint(geo.point.new(0, displayHeight)),
        point:distanceToPoint(geo.point.new(displayWidth, displayHeight))
    )
end

function playdate.rightButtonDown()
    local maxRadius = getDistanceToFarthestCorner(transitionPoint)

    local timer = playdate.timer.new(
        transitionDuration,
        -maxRadius,
        maxRadius)

    ...
```

Lastly, your players deserve better than linear easing.

```lua
    local timer = playdate.timer.new(
        transitionDuration,
        -maxRadius,
        maxRadius,
        playdate.easingFunctions.inOutQuad)
```

<video autoplay height="396" loop src="/videos/playdate-circle-transition-4.mp4" width="660"></video>

That's all folks!

---

[^1]: Sample project [on GitHub](https://github.com/mminer/playdate-circle-transition).
