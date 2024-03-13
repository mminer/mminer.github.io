---
title: "Recording Physics Animation in Unity"
---

*The Gardens Between* is a pretty game, but what grabbed my attention is a mechanic that simulates physics as you walk forward then reverses it when you backtrack, akin to scrubbing on a timeline.

<video autoplay height="371" loop src="/videos/gardens-between-popcorn.mp4" width="660"></video>

Eye-catching, right? I wasted many minutes walking back and forth watching popcorn explode then recoil into its bowl. The game has some story about childhood friendship, but I remember the popcorn.

Let's talk about how to achieve this effect in Unity.[^1] In lieu of kernels we'll use a pile of crates. We could animate each crate individually, but I have neither the talent nor patience to make this look convincing. Instead we can put the physics engine to work with a component that blasts them to high heaven.

```csharp
using UnityEngine;

class Explosion : MonoBehaviour
{
    public float force = 1_000f;
    public float radius = 10f;

    void Start()
    {
        var targets = FindObjectsByType<Rigidbody>(FindObjectsSortMode.None);

        foreach (var target in targets)
        {
            target.AddExplosionForce(force, transform.position, radius);
        }
    }
}
```

<video autoplay height="347" loop src="/videos/recorder-animation-physics.mp4" width="660"></video>

But how to reverse the simulation? This is where Recorder comes in. I've written about Recorder before — it's a handy way to [take screenshots](/2021/06/27/screenshots-with-unity-recorder) and [capture gameplay videos](/2021/05/23/big-image-recorder-for-unity). Less obviously, it can also record physics simulations as animation to later play on demand.

1. In the Recorder window, click *Add Recorder* and choose "Animation Clip".
1. Drag the crates' parent game object to the *GameObject* field.
1. Set *Recorded Components* to "Transform".
1. Check *Record Hierarchy*.
1. Click *START RECORDING*.
1. Click *STOP RECORDING* when you're satisfied with the result.

![Recorder animation clip](/images/recorder-animation-clip.png)

This saves a *.anim* file as a new asset. Crack this open to see a wall of keyframes — six per frame (position + rotation), for each crate. It's a hefty file.[^2]

![Animation clip keyframes](/images/animation-clip-keyframes.png)

At this point we can change our crate rigidbodies to kinematic and disable gravity. To see our animation in action we'll use a timeline.

1. Create a timeline from *Assets > Create > Timeline > Timeline*.
1. Drag this new asset to the Hierarchy window.
1. Drag the animation clip to the Timeline window.
1. Drag the crates' parent game object to the timeline track's "None (Animator)" field.
1. When prompted, choose "Create Animator on ExplosionTimeline".

If we wired this up correctly, we can hit play to see our recorded explosion.

<video autoplay height="347" loop src="/videos/recorder-animation-timeline.mp4" width="660"></video>

Finally, let's uncheck *Play On Awake* on the Playable Director and create our own component to control playback.

```csharp
using UnityEngine;
using UnityEngine.Playables;

class ExplosionPlayback : MonoBehaviour
{
    PlayableDirector director;

    void Start()
    {
        director = GetComponent<PlayableDirector>();
    }

    void Update()
    {
        if (Input.GetKey(KeyCode.LeftArrow))
        {
            director.time -= Time.deltaTime;
        }

        if (Input.GetKey(KeyCode.RightArrow))
        {
            director.time += Time.deltaTime;
        }

        director.Evaluate();
    }
}
```

Attach this to the game object containing the Playable Director, hit play, and scrub back and forth using the left and right arrow keys.

<video autoplay height="347" loop src="/videos/recorder-animation-scrubbing.mp4" width="660"></video>

Now that's satisfying.

---

[^1]: Code snippets [on GitHub](https://github.com/mminer/blog-code/tree/master/recording-physics-animation-in-unity).

[^2]: This small example resulted in a 4 MB animation clip. Prune keyframes if you don't need an exact frame-by-frame copy of your simulation.
