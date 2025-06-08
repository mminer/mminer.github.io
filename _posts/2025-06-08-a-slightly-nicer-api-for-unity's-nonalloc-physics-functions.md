---
title: "A Slightly Nicer API for Unity's <code>NonAlloc</code> Physics Functions"
---

If you're a Unity developer, you might use the allocation-free versions of its intersection functions — `Physics.RaycastNonAlloc` instead of `Physics.RaycastAll`, `Physics.OverlapBoxNonAlloc` instead of `Physics.OverlapBox`, and so forth. And if you don't, perhaps you ought to; they're an easy way to reduce garbage collection overhead in your game if you call these functions every frame.

A typical implementation looks like this:

```csharp
RaycastHit[] hits = new RaycastHit[10];

void Update()
{
    var hitCount = Physics.RaycastNonAlloc(transform.position, transform.forward, hits);

    for (var i = 0; i < hitCount; i++)
    {
        var hit = hits[i];
        Debug.Log("Hit: " + hit.collider.name);
    }
}
```

If you write this pattern often, a useful abstraction is to wrap the logic in a class and use [`ReadOnlySpan`](https://learn.microsoft.com/en-us/dotnet/api/system.readonlyspan-1) to expose the results.

```csharp
class Raycaster
{
    RaycastHit[] hits = new RaycastHit[10];

    ReadOnlySpan<RaycastHit> RaycastAll(Vector3 origin, Vector3 direction)
    {
        var hitCount = Physics.RaycastNonAlloc(origin, direction, hits);
        return new ReadOnlySpan<RaycastHit>(hits, 0, hitCount);
    }
}
```

`ReadOnlySpan` is a lightweight container that allows you to peek into a slice of a managed array. With this new class, we can now iterate over our raycast hits in a foreach loop; no need to manage a results array and no danger of misusing `hitCount`.

```csharp
Raycaster raycaster = new();

void Update()
{
    var hits = raycaster.RaycastAll(transform.position, transform.forward);

    foreach (var hit in hits)
    {
        Debug.Log("Hit: " + hit.collider.name);
    }
}
```

Simple as it is, I use this pattern enough that it was worthwhile to codify into a reusable package. After adding [nonalloc-physics-wrapper](https://github.com/mminer/nonalloc-physics-wrapper) to your game, prefix any `Physics` intersection function with `NonAlloc` and iterate over the return value as before.

```diff
- var hits = Physics.RaycastAll(ray);
+ var hits = NonAllocPhysics.RaycastAll(ray);

- var colliders = Physics.OverlapBox(center, halfExtents);
+ var colliders = NonAllocPhysics.OverlapBox(center, halfExtents);

... and so on.
```

Now instead of a new array being allocated on each call, a single one is reused across multiple calls and the garbage collector has that much less work to do. He deserves a break.
