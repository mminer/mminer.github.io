---
title: "Methodic: Run Functions by Clicking a Button"
---

Ages ago I built a small Unity editor extension that makes its way into every project I fire up. It allows you to run functions in a game object's components through a GUI. I recognize how dull that sentence is, so let me convince you that this is something you need with an example.[^1]

Suppose you're developing a Breakout clone. Each brick gets a component with an `Explode` function that, well, makes it explode.

```csharp
using UnityEngine;

class Brick : MonoBehaviour
{
    void Explode ()
    {
        // TODO: add impressive fireball
        Debug.Log("Exploding!");
        Destroy(gameObject);
    }
}
```

Nice job. Let's test your handiwork. There are plenty of ways to run the function, but Methodic provides the easiest. Click the brick game object, open *Window > Methodic*, hit "Invoke", and voilà: your brick detonates in a fiery blast.

![Methodic Window](/images/methodic.png)

No extra code necessary. Even groovier, if your function takes arguments, you can specify these using familiar checkboxes and text fields and colour pickers.

![Methodic Window With Arguments](/images/methodic-arguments.png)

Public, private, static, or instance function, Methodic runs them all. You can even execute functions when Unity is in edit mode.[^2] Still yawning? A video sells it better.

<div class="video">
    <iframe allow="fullscreen" src="https://www.youtube-nocookie.com/embed/x9x80XV-8G8?modestbranding=1">
        <a href="https://www.youtube.com/watch?v=x9x80XV-8G8">
            Methodic: Run Functions by Clicking a Button
        </a>
    </iframe>
</div>

If this looks like something your game development environment pines for (or if you want to make me a wealthy man), you can [purchase Methodic](https://assetstore.unity.com/packages/tools/utilities/methodic-954) for $10 on the Unity Asset Store. I can't promise that you'll make better games with it, but you *might*, and is that an opportunity you can ignore? Not in this economy.

---

[^1]: Assets for my Breakout clone from [Kenney's Puzzle Pack 2](http://www.kenney.nl/assets/puzzle-pack-2). Thanks Kenney!

[^2]: Unity complains when running functions like `Object.Destroy` that cannot be called in edit mode. This makes sense though --- destructive changes are best left for play mode.
