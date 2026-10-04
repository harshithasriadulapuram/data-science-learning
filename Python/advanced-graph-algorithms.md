
# Advanced Graph Algorithms in Python

## 1. Introduction

Graph algorithms help us solve problems involving connections between objects.

A graph consists of:
- **Vertices (nodes):** The objects.
- **Edges:** The connections between objects.
- **Weights:** Optional values representing distance, cost, or time.

Graphs can be directed or undirected, weighted or unweighted.

Common applications include:
- GPS navigation
- Computer networks
- Social networks
- Delivery route optimization
- Network design
- Dependency management

This file covers:
1. Dijkstra's shortest-path algorithm
2. Bellman-Ford algorithm
3. Floyd-Warshall algorithm
4. Minimum Spanning Trees
5. Prim's algorithm
6. Kruskal's algorithm

---

## 2. Dijkstra's Shortest-Path Algorithm

### What Is Dijkstra's Algorithm?

Dijkstra's algorithm finds the shortest distances from one source vertex to all reachable vertices in a weighted graph with **non-negative edge weights**.

It uses a greedy strategy and a priority queue.

### Example Graph

```text
0 --4-- 1
|       |
1       2
|       |
2 --5-- 3
```

The edge weights represent travel costs.

Dijkstra repeatedly selects the vertex with the smallest currently known distance and attempts to improve the distances to its neighbors.

### Python Implementation

```python
import heapq


def dijkstra(graph, source):
    distances = {node: float("inf") for node in graph}
    distances[source] = 0

    min_heap = [(0, source)]

    while min_heap:
        current_distance, node = heapq.heappop(min_heap)

        # Ignore outdated heap entries.
        if current_distance != distances[node]:
            continue

        for neighbor, weight in graph[node]:
            new_distance = current_distance + weight

            if new_distance < distances[neighbor]:
                distances[neighbor] = new_distance

                heapq.heappush(
                    min_heap,
                    (new_distance, neighbor)
                )

    return distances


graph = {
    0: [(1, 4), (2, 1)],
    1: [(0, 4), (2, 2), (3, 1)],
    2: [(0, 1), (1, 2), (3, 5)],
    3: [(1, 1), (2, 5)]
}

print(dijkstra(graph, 0))
```

Expected output:

```text
{0: 0, 1: 3, 2: 1, 3: 4}
```

The dictionary gives the shortest distance from source `0` to each vertex.

### Important Limitation

Dijkstra's algorithm is not correct for graphs with negative edge weights in general.

Use Bellman-Ford when negative weights may occur.

### Complexity

With an adjacency list and a binary heap:
- Time: O((V + E) log V)
- Space: O(V + E), including the graph representation

Here, V is the number of vertices and E is the number of edges.

---

## 3. Bellman-Ford Algorithm

### What Is Bellman-Ford?

Bellman-Ford finds shortest distances from a single source, even when some edges have negative weights.

It can also detect a negative-weight cycle reachable from the source.

A negative-weight cycle is a cycle whose total edge weight is negative. In such a cycle, a shortest path may not have a finite minimum because repeatedly traversing the cycle reduces the total cost.

### How It Works

1. Initialize the source distance to zero.
2. Initialize all other distances to infinity.
3. Relax every edge up to V - 1 times.
4. Stop early if an entire pass makes no changes.
5. Make one additional pass to check for a reachable negative-weight cycle.

**Relaxation** means improving a distance when a cheaper route is found.

### Python Implementation

```python
def bellman_ford(vertices, edges, source):
    distances = [float("inf")] * vertices
    distances[source] = 0

    # Relax all edges up to V - 1 times.
    for _ in range(vertices - 1):
        changed = False

        for u, v, weight in edges:
            if distances[u] == float("inf"):
                continue

            new_distance = distances[u] + weight

            if new_distance < distances[v]:
                distances[v] = new_distance
                changed = True

        if not changed:
            break

    # Detect a negative-weight cycle reachable from source.
    for u, v, weight in edges:
        if distances[u] == float("inf"):
            continue

        if distances[u] + weight < distances[v]:
            return None

    return distances


edges = [
    (0, 1, 4),
    (0, 2, 5),
    (1, 2, -2),
    (2, 3, 3)
]

result = bellman_ford(4, edges, 0)
print(result)
```

Expected output:

```text
[0, 4, 2, 5]
```

The `None` result indicates that a negative-weight cycle is reachable from the source.

### Complexity

- Time: O(VE)
- Extra space: O(V), excluding the input edge list

### Dijkstra vs Bellman-Ford

| Feature | Dijkstra | Bellman-Ford |
|---|---|---|
| Negative edges | Not supported in general | Supported |
| Detects reachable negative cycles | No | Yes |
| Typical speed | Faster | Slower |
| Main technique | Greedy + priority queue | Repeated edge relaxation |

---

## 4. Floyd-Warshall Algorithm

### What Is Floyd-Warshall?

Floyd-Warshall calculates the shortest distances between **all pairs of vertices**.

Unlike Dijkstra, which starts from one source, Floyd-Warshall computes a distance matrix covering every pair.

It supports negative edge weights, but not negative-weight cycles if meaningful finite shortest distances are required.

### Core Idea

For every possible intermediate vertex `k`, check whether travelling from `i` to `j` through `k` is cheaper than the current known route.

The update rule is:

```text
dist[i][j] = min(
    dist[i][j],
    dist[i][k] + dist[k][j]
)
```

### Python Implementation

```python
def floyd_warshall(dist):
    n = len(dist)

    for k in range(n):
        for i in range(n):
            for j in range(n):
                if (
                    dist[i][k] != float("inf")
                    and dist[k][j] != float("inf")
                ):
                    dist[i][j] = min(
                        dist[i][j],
                        dist[i][k] + dist[k][j]
                    )

    return dist


graph = [
    [0, 3, float("inf")],
    [float("inf"), 0, 2],
    [1, float("inf"), 0]
]

distances = floyd_warshall(graph)

for row in distances:
    print(row)
```

The matrix uses infinity to represent the absence of a direct edge.

**Important:** The function modifies the supplied matrix. To check for a negative-weight cycle, inspect whether any diagonal entry becomes negative after the algorithm finishes.

### Complexity

- Time: O(V³)
- Space: O(V²)

Floyd-Warshall is useful when the graph is relatively small and shortest distances between many pairs are needed.

---

## 5. Minimum Spanning Tree (MST)

### What Is a Minimum Spanning Tree?

A Minimum Spanning Tree is a subset of edges in a connected, weighted, undirected graph that:

- Connects every vertex.
- Contains no cycles.
- Minimizes the total edge weight.
- Contains exactly V - 1 edges when there are V vertices.

Applications include:
- Network cable design
- Electrical grid planning
- Road network planning
- Connecting distributed systems at minimum cost

Two common MST algorithms are:
1. Prim's algorithm
2. Kruskal's algorithm

For disconnected graphs, these algorithms can instead produce a minimum spanning forest.

---

## 6. Prim's Algorithm

### What Is Prim's Algorithm?

Prim's algorithm builds an MST by repeatedly choosing the minimum-weight edge that connects a vertex already in the tree to a vertex outside it.

It uses a greedy strategy.

### Python Implementation

```python
import heapq


def prim_mst(graph, start=0):
    if start not in graph:
        raise ValueError("Start vertex is not in the graph")

    visited = set()
    min_heap = [(0, start, None)]
    mst_edges = []
    total_weight = 0

    while min_heap:
        weight, node, parent = heapq.heappop(min_heap)

        if node in visited:
            continue

        visited.add(node)

        if parent is not None:
            mst_edges.append((parent, node, weight))
            total_weight += weight

        for neighbor, edge_weight in graph[node]:
            if neighbor not in visited:
                heapq.heappush(
                    min_heap,
                    (edge_weight, neighbor, node)
                )

    if len(visited) != len(graph):
        raise ValueError("Graph is disconnected")

    return mst_edges, total_weight


graph = {
    0: [(1, 2), (3, 6)],
    1: [(0, 2), (2, 3), (3, 8)],
    2: [(1, 3), (3, 1)],
    3: [(0, 6), (1, 8), (2, 1)]
}

edges, total = prim_mst(graph)

print(edges)
print("Total weight:", total)
```

Expected total weight:

```text
Total weight: 6
```

### Complexity

With an adjacency list and binary heap:
- Time: O(E log V)
- Space: O(V + E), including the graph

Prim's algorithm assumes an undirected graph represented with consistent edges in both directions.

---

## 7. Kruskal's Algorithm

### What Is Kruskal's Algorithm?

Kruskal's algorithm builds an MST by sorting edges by weight and considering the cheapest edges first.

It uses a Disjoint Set Union (DSU) structure to avoid creating cycles.

### Steps

1. Sort edges by increasing weight.
2. Start with each vertex in its own set.
3. Process each edge in sorted order.
4. Add an edge if its endpoints belong to different sets.
5. Merge their sets using DSU.
6. Stop after selecting V - 1 edges.

### Python Implementation

```python
def kruskal_mst(vertices, edges):
    parent = list(range(vertices))
    size = [1] * vertices

    def find(x):
        if parent[x] != x:
            parent[x] = find(parent[x])
        return parent[x]

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

    mst_edges = []
    total_weight = 0

    for u, v, weight in sorted(edges, key=lambda edge: edge[2]):
        if union(u, v):
            mst_edges.append((u, v, weight))
            total_weight += weight

            if len(mst_edges) == vertices - 1:
                break

    if len(mst_edges) != vertices - 1:
        raise ValueError("Graph is disconnected")

    return mst_edges, total_weight


edges = [
    (0, 1, 2),
    (0, 3, 6),
    (1, 2, 3),
    (1, 3, 8),
    (2, 3, 1)
]

mst, total = kruskal_mst(4, edges)

print(mst)
print("Total weight:", total)
```

Expected total weight:

```text
Total weight: 6
```

### Complexity

- Time: O(E log E), dominated by sorting the edges.
- Space: O(V + E), including the input edge list and DSU.

### Prim vs Kruskal

| Feature | Prim | Kruskal |
|---|---|---|
| Main strategy | Grow one tree | Select edges globally in weight order |
| Main data structure | Priority queue | DSU |
| Main operation | Choose cheapest outgoing edge | Choose cheapest edge that joins different components |
| Typical representation | Adjacency list | Edge list |
| Disconnected graph | Requires a forest variant | Naturally adaptable to a forest variant |

---

## 8. Choosing the Right Graph Algorithm

| Problem | Suitable algorithm |
|---|---|
| Shortest path from one source, non-negative weights | Dijkstra |
| Shortest path from one source, possibly negative edges | Bellman-Ford |
| Shortest paths between every pair | Floyd-Warshall |
| Minimum spanning tree | Prim or Kruskal |
| Cycle detection in an undirected graph | DSU or DFS |
| Connectivity after adding edges | DSU |

Always identify whether the graph is directed or undirected and whether its weights can be negative before choosing an algorithm.

---

## 9. Common Interview Questions

1. What is the difference between BFS and Dijkstra?
2. Why does Dijkstra fail with negative edge weights?
3. What is edge relaxation?
4. How does Bellman-Ford detect a negative cycle?
5. What is the difference between single-source and all-pairs shortest paths?
6. What is a Minimum Spanning Tree?
7. How do Prim and Kruskal differ?
8. Why does Kruskal use DSU?
9. What is the time complexity of Floyd-Warshall?
10. Can a graph have more than one MST?
11. What happens when a weighted graph is disconnected?
12. What is the difference between a shortest-path tree and an MST?

---

## 10. Practice Problems

### Beginner
1. Implement Dijkstra's algorithm.
2. Calculate shortest distances using Bellman-Ford.
3. Find the MST using Kruskal's algorithm.

### Intermediate
4. Detect a negative-weight cycle.
5. Implement Floyd-Warshall.
6. Compare Prim and Kruskal on the same graph.
7. Reconstruct an actual shortest path, not just its distance.

### Advanced
8. Solve Network Delay Time using Dijkstra.
9. Solve Cheapest Flights Within K Stops.
10. Find the minimum cost to connect all points.
11. Solve a graph problem involving negative cycles.
12. Compare shortest-path algorithms on different graph types.

## Key Takeaways

- Dijkstra handles shortest paths with non-negative weights.
- Bellman-Ford supports negative edges and detects reachable negative-weight cycles.
- Floyd-Warshall computes all-pairs shortest distances.
- Prim and Kruskal construct Minimum Spanning Trees.
- DSU helps Kruskal efficiently avoid cycles.
- The best algorithm depends on graph structure, edge weights, and the question being asked.
