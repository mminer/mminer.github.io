---
title: "Global Game Jam: Pathfinding"
---

Along with [fog of war](/2021/02/21/global-game-jam-fog-of-war), another challenge I tackled for our 2021 Global Game Jam submission was pathfinding.

There are a slew of Unity Asset Store packages that provide pathfinding. Like [this one](https://assetstore.unity.com/packages/tools/ai/a-pathfinding-project-pro-87744) that has been around for eternity. If you have more complex requirements than I did --- say, continuous rather than grid-based movement[^1] --- it's worth your time to evaluate these. A basic A* implementation was all I needed though, so I went the DIY route instead of the drudgery of integrating a third party solution.

A* is a tried and true classic, the kind of algorithm you study in computer science class. [The Wikipedia article](https://en.wikipedia.org/wiki/A*_search_algorithm) is a good overview, but give Red Blobs Games' [Introduction to the A* Algorithm](https://www.redblobgames.com/pathfinding/a-star/introduction.html) a read for an even better explanation.

Here's my version, a line-by-line translation of the Wikipedia pseudocode:[^2]

```csharp
List<T> FindShortestPath<T>(
    T start,
    T goal,
    Func<T, int> getCostEstimate,
    Func<T, T, int> getEdgeWeight,
    Func<T, IEnumerable<T>> getNeighbors) where T : IEquatable<T>
{
    // The set of discovered nodes that may need to be (re-)expanded.
    // Initially, only the start node is known.
    // This is usually implemented as a min-heap or priority queue rather than a hash-set.
    var openSet = new HashSet<T> {start};

    // For node n, cameFrom[n] is the node immediately preceding it on the cheapest path from start to n currently known.
    var cameFrom = new Dictionary<T, T>();

    // For node n, gScore[n] is the cost of the cheapest path from start to n currently known.
    var gScore = new Dictionary<T, int>();
    gScore[start] = 0;

    // For node n, fScore[n] = gScore[n] + getCostEstimate(n).
    // fScore[n] represents our current best guess as to how short a path from start to finish can be if it goes through n.
    var fScore = new Dictionary<T, int>();
    fScore[start] = getCostEstimate(start);

    while (openSet.Count > 0)
    {
        // Find the node in openSet with the lowest fScore value.
        // This operation can occur in O(1) time if openSet is a min-heap or a priority queue.
        var current = openSet
            .OrderBy(position => fScore.TryGetValue(position, out var fValue) ? fValue : int.MaxValue)
            .First();

        if (current.Equals(goal))
        {
            return ReconstructPath(cameFrom, current);
        }

        openSet.Remove(current);
        var neighbors = getNeighbors(current);

        foreach (var neighbor in neighbors)
        {
            // tentativeGScore is the distance from start to the neighbor through current.
            var tentativeGScore = gScore[current]+ getEdgeWeight(current, neighbor);
            var neighborGScore = gScore.TryGetValue(neighbor, out var gValue) ? gValue : int.MaxValue;

            if (tentativeGScore < neighborGScore)
            {
                // This path to neighbor is better than any previous one. Record it!
                cameFrom[neighbor] = current;
                gScore[neighbor] = tentativeGScore;
                fScore[neighbor] = gScore[neighbor] + getCostEstimate(neighbor);

                if (!openSet.Contains(neighbor))
                {
                    openSet.Add(neighbor);
                }
            }
        }
    }

    // Open set is empty but goal was never reached.
    return null;
}

List<T> ReconstructPath<T>(Dictionary<T, T> cameFrom, T current)
{
    var totalPath = new List<T> {current};

    while (cameFrom.ContainsKey(current))
    {
        current = cameFrom[current];
        totalPath.Insert(0, current);
    }

    return totalPath;
}
```

To use it you pass five arguments. `start` is where you begin and `goal` is where you want to end up. `getEdgeWeight` determines how expensive it is to travel from a node to its neighbour. Our game only had one ground tile type so we returned a constant of `1`, but you might want to make some terrain (e.g. mud) slower to traverse.

`getNeighbours` is self-explanatory: given a node, return a list of nodes adjacent to it. `getCostEstimate` is a heuristic function that returns how expensive it is to travel from a given node to the goal. For a grid, this can simply be horizontal + vertical distance. Our calling function wound up looking like this:

```csharp
List<Vector3Int> GetPathFromEnemyToPlayer(Transform enemy, Transform player)
{
    var start = Vector3Int.RoundToInt(enemy.position);
    var goal = Vector3Int.RoundToInt(player.position);
    int getCostEstimate(Vector3Int position) => Mathf.Abs(goal.x - position.x) + Mathf.Abs(goal.z - position.z);
    int getEdgeWeight(Vector3Int current, Vector3Int neighbour) => 1;
    List<Vector3Int> getNeighbors(Vector3Int current) => level.GetAdjacentPositions(current);
    return FindShortestPath(start, goal, getCostEstimate, getEdgeWeight, getNeighbors);
}
```

If this would be helpful in your project, please take it and use it friend. Sharing is caring.

---

[^1]: If your game lends itself to grid-based movement, praise be. It frees you from so many fiddly edge cases. Pathfinding is no exception.

[^2]: [In Gist form](https://gist.github.com/mminer/0cd9a996e8fc817482a79a3ccb70655a).
