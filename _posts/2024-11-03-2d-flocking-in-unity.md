---
title: "2D Flocking in Unity"
---

[Flocking](https://en.wikipedia.org/wiki/Flocking) implementations are plentiful. Here's mine.[^1]

<video autoplay height="413" loop src="/videos/flocking.mp4" width="660"></video>

```csharp
using UnityEngine;

public class Flock : MonoBehaviour
{
    [SerializeField, Min(0)] float spawnRadius = 5f;
    [SerializeField, Min(1)] int unitCount = 50;
    [SerializeField] Transform unitPrefab;

    [Header("Unit")]
    [SerializeField, Range(0, 360)] float fieldOfView = 270f;
    [SerializeField, Min(0)] float separationRadius = 1f;
    [SerializeField, Min(0)] float sightRadius = 3f;
    [SerializeField, Min(0)] float speed = 1f;
    [SerializeField, Min(0)] float steeringForce = 1f;

    Transform[] units;

    void Awake()
    {
        InstantiateUnits();
    }

    void Update()
    {
        foreach (var unit in units)
        {
            UpdateUnit(unit);
        }
    }

    void InstantiateUnits()
    {
        units = new Transform[unitCount];

        for (var i = 0; i < unitCount; i++)
        {
            var position = Random.insideUnitCircle * spawnRadius;
            var rotation = Quaternion.Euler(0, 0, Random.Range(0, 360));
            units[i] = Instantiate(unitPrefab, position, rotation, transform);
        }
    }

    void UpdateUnit(Transform unit)
    {
        var unitPosition = (Vector2)unit.position;
        var unitDirection = (Vector2)unit.up;

        var neighborCount = 0;
        var averageNeighborPosition = Vector2.zero;
        var averageNeighborDirection = Vector2.zero;
        var separation = Vector2.zero;

        foreach (var otherUnit in units)
        {
            if (otherUnit == unit)
            {
                continue;
            }

            var otherUnitPosition = (Vector2)otherUnit.position;
            var otherUnitDirection = (Vector2)otherUnit.up;

            var directionToOther = otherUnitPosition - unitPosition;
            var sqrDistanceToOther = directionToOther.sqrMagnitude;
            var isWithinSightRadius = sqrDistanceToOther <= sightRadius * sightRadius;

            if (!isWithinSightRadius)
            {
                continue;
            }

            var angleToOther = Vector2.Angle(unitDirection, directionToOther);
            var isWithinFieldOfView = angleToOther <= fieldOfView / 2;

            if (!isWithinFieldOfView)
            {
                continue;
            }

            neighborCount++;
            averageNeighborPosition += otherUnitPosition;
            averageNeighborDirection += otherUnitDirection;

            var isWithinSeparationRadius = sqrDistanceToOther <= separationRadius * separationRadius;

            if (isWithinSeparationRadius)
            {
                separation -= directionToOther * separationRadius / sqrDistanceToOther;
            }

            Debug.DrawLine(unitPosition, otherUnitPosition, Color.green);
        }

        if (neighborCount > 0)
        {
            averageNeighborPosition /= neighborCount;
            averageNeighborDirection /= neighborCount;

            var cohesion = (averageNeighborPosition - unitPosition).normalized;
            var alignment = averageNeighborDirection.normalized;
            unit.up = Vector3.Lerp(unit.up, cohesion + alignment + separation, steeringForce * Time.deltaTime);
        }

        unit.position += unit.up * (speed * Time.deltaTime);
    }
}
```

Here's an `OnDrawGizmos` function to visualize the field of view and other parameters.

<video autoplay height="413" loop src="/videos/flocking-gizmos.mp4" width="660"></video>

```csharp
void OnDrawGizmos()
{
    if (units == null)
    {
        return;
    }

    Handles.color = Color.red;

    foreach (var unit in units)
    {
        Handles.DrawLine(unit.position, unit.position + (unit.up * sightRadius));

        var arcStartDirection = Quaternion.Euler(0, 0, -fieldOfView / 2) * unit.up;
        var arcEndDirection = Quaternion.Euler(0, 0, fieldOfView / 2) * unit.up;
        Handles.DrawWireArc(unit.position, Vector3.forward, arcStartDirection, fieldOfView, separationRadius);
        Handles.DrawWireArc(unit.position, Vector3.forward, arcStartDirection, fieldOfView, sightRadius);

        var arcStartPosition = unit.position + (arcStartDirection * sightRadius);
        var arcEndPosition = unit.position + (arcEndDirection * sightRadius);
        Handles.DrawLine(unit.position, arcStartPosition);
        Handles.DrawLine(unit.position, arcEndPosition);
    }
}
```

To write my version I followed along with [*AI for Game Developers*](https://www.oreilly.com/library/view/ai-for-game/0596005555/). At 20 years old it's a senior citizen of a tech reference, but it explains the algorithm well.

Performance degrades quickly as you increase the number of units. Spawn 1,000 units and watch the frame rate plummet. The algorithm is an ideal candidate for Unity's job system to split the work across multiple threads, but jobs (and multithreading generally) are unsupported on WebGL, the platform I'm targeting. Alas.

A better approach is to reduce the number of neighbour checks. The Internet tells me that with spatial partitioning we can knock the complexity from *O*(*n*<sup>2</sup>) down to *O*(*n*). A fine next step.

---

[^1]: Complete code [on GitHub](https://gist.github.com/mminer/227ce5f4eaaf70d382e6823118ae031f).
