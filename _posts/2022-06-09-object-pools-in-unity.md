---
title: "Object Pools in Unity"
---

Originally I was going to write a post singing the praises of [Lean Pool](https://assetstore.unity.com/packages/tools/utilities/lean-pool-35666). It's everything a Unity package should be --- a sensible API, regularly maintained and thoroughly documented. And it's free. It's hard to argue with free.

If you use Unity 2020 or earlier, look no further than Lean Pool for your object pooling needs. But as of Unity 2021 there's a new built-in option: [`ObjectPool`](https://docs.unity3d.com/ScriptReference/Pool.ObjectPool_1.html)

Let's explore the API by creating the world's lousiest rain system. We'll start with a "droplet" component that moves a game object downward at a constant rate.

```csharp
class Droplet : MonoBehaviour
{
    void Start()
    {
        Debug.Log("New droplet");
    }

    void Update()
    {
        transform.Translate(Vector3.down * Time.deltaTime * 10);
    }
}
```

Now for a component to spawn these droplets. Instead of instantiating and destroying game objects repeatedly, we instead recycle them with an `ObjectPool`. Its interface is minimal; pass a function that creates an object to its constructor, call `Get` to acquire an object, then `Release` to return it.

```csharp
class RainSystem : MonoBehaviour
{
    ObjectPool<GameObject> pool;

    IEnumerator Start()
    {
        pool = new ObjectPool<GameObject>(() =>
        {
            var go = GameObject.CreatePrimitive(PrimitiveType.Sphere);
            go.AddComponent<Droplet>();
            return go;
        });

        // Spawn new droplets repeatedly.
        while (true)
        {
            StartCoroutine(SpawnDroplet());
            yield return new WaitForSeconds(0.5f);
        }
    }

    IEnumerator SpawnDroplet()
    {
        var go = pool.Get();
        go.transform.position = Random.insideUnitSphere * 10;
        yield return new WaitForSeconds(3);
        pool.Release(go);
    }
}
```

Observe that the console stops printing "New droplet" after a few times, because no new instances are being created. `ObjectPool` is working its magic.

<video autoplay height="302" loop src="/videos/object-pool.mp4" width="660"></video>

That's it. Object pools --- that [basic but essential](https://gameprogrammingpatterns.com/object-pool.html) pattern you've implemented a dozen times, now available out of the box.
