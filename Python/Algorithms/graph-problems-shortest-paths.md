
# Graph Problems: Shortest Paths and Connectivity

This file covers important graph algorithms used in coding interviews and competitive programming.

## 1. Dijkstra's Algorithm

**Problem:** Find the shortest distances from a source vertex to all other vertices in a weighted graph with non-negative edge weights.

Dijkstra's algorithm uses a min-heap to process the closest unprocessed vertex first.

```python
import heapq

def dijkstra(graph, start):
    distances = {node: float("inf") for node in graph}
    distances[start] = 0
    min_heap = [(0, start)]

    while min_heap:
        distance, node = heapq.heappop(min_heap)

        if distance != distances[node]:
            continue

        for neighbor, weight in graph[node]:
            new_distance = distance + weight

            if new_distance < distances.get(neighbor, float("inf")):
                distances[neighbor] = new_distance
                heapq.heappush(
                    min_heap, (new_distance, neighbor)
                )

    return distances
```

Example:

```python
weighted_graph = {
    "A": [("B", 4), ("C", 1)],
    "B": [("D", 1)],
    "C": [("B", 2), ("D", 5)],
    "D": []
}

print(dijkstra(weighted_graph, "A"))
# {'A': 0, 'B': 3, 'C': 1, 'D': 4}
```

**Complexity:**
- Time: `O((V + E) log V)` with an adjacency list and binary heap.
- Space: `O(V + E)` including the graph representation.

**Important:** Dijkstra's algorithm does not correctly handle arbitrary negative-weight edges.

---

## 2. Bellman-Ford Algorithm

**Problem:** Find shortest distances from a source in a weighted graph, including graphs with negative-weight edges. Detect a reachable negative-weight cycle.

```python
def bellman_ford(vertices, edges, source):
    distances = {v: float("inf") for v in vertices}
    distances[source] = 0

    for _ in range(len(vertices) - 1):
        changed = False

        for u, v, weight in edges:
            if distances[u] != float("inf"):
                candidate = distances[u] + weight

                if candidate < distances[v]:
                    distances[v] = candidate
                    changed = True

        if not changed:
            break

    for u, v, weight in edges:
        if (
            distances[u] != float("inf")
            and distances[u] + weight < distances[v]
        ):
            return None  # Reachable negative-weight cycle.

    return distances
```

Example:

```python
vertices = [0, 1, 2, 3]
edges = [
    (0, 1, 4),
    (0, 2, 5),
    (1, 2, -2),
    (2, 3, 3)
]

print(bellman_ford(vertices, edges, 0))
# {0: 0, 1: 4, 2: 2, 3: 5}
```

**Complexity:**
- Time: `O(V * E)`
- Space: `O(V)`, excluding the input graph.

---

## 3. Floyd-Warshall Algorithm

**Problem:** Find shortest distances between every pair of vertices.

This algorithm uses dynamic programming and is suitable for relatively small, dense graphs.

```python
def floyd_warshall(distances):
    dist = [row[:] for row in distances]
    n = len(dist)

    for k in range(n):
        for i in range(n):
            for j in range(n):
                dist[i][j] = min(
                    dist[i][j],
                    dist[i][k] + dist[k][j]
                )

    return dist
```

Use `float("inf")` when no direct edge exists and `0` on the diagonal.

Example:

```python
INF = float("inf")

matrix = [
    [0,   3,   10],
    [INF, 0,   2],
    [INF, INF, 0]
]

print(floyd_warshall(matrix))
# [[0, 3, 5], [inf, 0, 2], [inf, inf, 0]]
```

**Complexity:**
- Time: `O(V^3)`
- Space: `O(V^2)`.

---

## 4. Minimum Spanning Tree: Kruskal's Algorithm

**Problem:** Connect every vertex of a connected, undirected, weighted graph with minimum total edge weight and no cycles.

Kruskal's algorithm sorts edges by weight and uses Disjoint Set Union (DSU) to avoid cycles.

```python
def kruskal(vertices, edges):
    parent = {v: v for v in vertices}
    size = {v: 1 for v in vertices}

    def find(x):
        while parent[x] != x:
            parent[x] = parent[parent[x]]
            x = parent[x]
        return x

    def union(a, b):
        root_a = find(a)
        root_b = find(b)

        if root_a == root_b:
            return False

        if size[root_a] < size[root_b]:
            root_a, root_b = root_b, root_a

        parent[root_b] = root_a
        size[root_a] += size[root_b]
        return True

    mst = []
    total_weight = 0

    for u, v, weight in sorted(edges, key=lambda edge: edge[2]):
        if union(u, v):
            mst.append((u, v, weight))
            total_weight += weight

    if len(mst) != len(vertices) - 1:
        return None  # Graph is disconnected.

    return mst, total_weight
```

Example:

```python
vertices = [0, 1, 2, 3]
edges = [
    (0, 1, 10),
    (0, 2, 6),
    (0, 3, 5),
    (1, 3, 15),
    (2, 3, 4)
]

print(kruskal(vertices, edges))
# ([(2, 3, 4), (0, 3, 5), (0, 1, 10)], 19)
```

**Complexity:**
- Time: `O(E log E)` due to sorting.
- Space: `O(V)` for DSU, excluding the input edges.

---

## 5. Minimum Spanning Tree: Prim's Algorithm

**Problem:** Build a minimum spanning tree by repeatedly adding the minimum-weight edge that connects the growing tree to a new vertex.

```python
import heapq

def prim(graph, start):
    visited = set()
    min_heap = [(0, start, None)]
    mst = []
    total_weight = 0

    while min_heap:
        weight, node, parent = heapq.heappop(min_heap)

        if node in visited:
            continue

        visited.add(node)
        total_weight += weight

        if parent is not None:
            mst.append((parent, node, weight))

        for neighbor, edge_weight in graph.get(node, []):
            if neighbor not in visited:
                heapq.heappush(
                    min_heap,
                    (edge_weight, neighbor, node)
                )

    if len(visited) != len(graph):
        return None  # Graph is disconnected.

    return mst, total_weight
```

**Complexity:**
- Time: `O(E log E)` with this heap implementation.
- Space: `O(V + E)` including the graph and heap.

---

## 6. When to Use Each Algorithm

| Algorithm | Best use |
|---|---|
| BFS | Shortest paths in unweighted graphs |
| Dijkstra | Single-source shortest paths with non-negative weights |
| Bellman-Ford | Single-source shortest paths when negative edges may exist |
| Floyd-Warshall | All-pairs shortest paths |
| Kruskal | Minimum spanning tree using sorted edges and DSU |
| Prim | Minimum spanning tree grown from a starting vertex |

## Interview Revision Checklist

- [ ] Explain why Dijkstra cannot handle arbitrary negative edges.
- [ ] Detect a reachable negative cycle with Bellman-Ford.
- [ ] Explain Floyd-Warshall's three nested loops.
- [ ] Define a minimum spanning tree.
- [ ] Compare Kruskal and Prim.
- [ ] Explain how DSU helps Kruskal avoid cycles.
- [ ] State the time and space complexity of each algorithm.
