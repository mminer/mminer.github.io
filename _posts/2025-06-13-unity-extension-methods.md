---
title: "Unity Extension Methods"
---

Speaking of [Unity packages](/2025/06/08/a-slightly-nicer-api-for-unity's-nonalloc-physics-functions), here's one I add to every new project: [Unity Extensions](https://github.com/mminer/unity-extensions). Whenever I find myself wishing an API existed for a `UnityEngine` type, I add it to this package as an extension method. The result is a small collection of utilities that make working with `Transform`, `Vector3`, and so forth more pleasant.

Say you want to set the Y position of your player to zero. You might write this:

```csharp
transform.position = new Vector3(transform.position.x, 0, transform.position.z);

// Or:
var position = transform.position;
position.y = 0;
transform.position = position
```

With the `Transform.SetY` extension method, you can write this instead:

```csharp
transform.SetY(0);
```

Another example. Given a list of enemy positions, you want to find the one closest to your player. Not difficult, but you may appreciate this short version:

```csharp
var closestEnemyPosition = transform.position.GetClosest(enemyPositions);
```

Take a gander at the
[README](https://github.com/mminer/unity-extensions?tab=readme-ov-file#extensions) for a full list of extension methods.

Exciting? No. Useful? You bet.
