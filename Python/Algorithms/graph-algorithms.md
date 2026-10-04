
# Graph Algorithms in Python

## 1. Introduction

Graph algorithms solve problems involving connected vertices and edges.

Important algorithms include:

- Dijkstra's algorithm
- Bellman-Ford algorithm
- Topological sorting
- Kruskal's algorithm
- Prim's algorithm
- Union-Find (Disjoint Set Union)

Let:
- V = number of vertices
- E = number of edges

## 2. Dijkstra's Algorithm

Dijkstra's algorithm finds shortest distances from a source vertex to other vertices in a weighted graph with **non-negative edge weights**.

It uses a priority queue to process the vertex with the smallest currently known distance.

### Example

```text
A --4-- B
|       |
1       2
|       |
C --5-- D
```

The shortest path from A to B is A → C → D → B if those edges and weights are present in the graph. Always calculate paths from the actual edge list rather than relying on the drawing alone.

### Python Implementation

```python
import heapq


def dijkstra(graph, start):
    distances = {vertex: float("inf") for vertex in graph}
    distances[start] = 0

    priority_queue = [(0, start)]

    while priority_queue:
        current_distance, vertex = heapq.heappop(priority_queue)

        # Ignore outdated queue entries
        if current_distance > distances[vertex]:
            continue

        for neighbor, weight in graph[vertex]:
            new_distance = current_distance + weight

            if new_distance < distances[neighbor]:
                distances[neighbor] = new_distance
                heapq.heappush(
                    priority_queue,
                    (new_distance, neighbor)
                )

    return distances


graph = {
    "A": [("B", 4), ("C", 1)],
    "B": [("A", 4), ("C", 2), ("D", 1)],
    "C": [("A", 1), ("B", 2), ("D", 5)],
    "D": [("B", 1), ("C", 5)]
}

print(dijkstra(graph, "A"))
# {'A': 0, 'B': 3, 'C': 1, 'D': 4}
```

### How It Works

1. Set the source distance to zero.
2. Set all other distances to infinity.
3. Add the source to a min-priority queue.
4. Remove the vertex with the smallest distance.
5. Relax its outgoing edges: update a neighbor when a shorter route is found.
6. Repeat until the queue is empty.

### Complexity

With an adjacency list and a binary heap, the typical complexity of this implementation is O((V + E) log V), assuming the graph has no more than a reasonable number of edges relative to its vertices.

**Important:** Dijkstra's algorithm is not suitable for graphs with negative edge weights.

## 3. Bellman-Ford Algorithm

Bellman-Ford finds shortest distances from a source and supports negative edge weights.

It can also detect negative-weight cycles reachable from the source.

```python
def bellman_ford(vertices, edges, start):
    distances = {vertex: float("inf") for vertex in vertices}
    distances[start] = 0

    # Relax all edges V - 1 times
    for _ in range(len(vertices) - 1):
        changed = False

        for u, v, weight in edges:
            if distances[u] == float("inf"):
                continue

            candidate = distances[u] + weight

            if candidate < distances[v]:
                distances[v] = candidate
                changed = True

        if not changed:
            break

    # Check for a reachable negative-weight cycle
    for u, v, weight in edges:
        if (
            distances[u] != float("inf")
            and distances[u] + weight < distances[v]
        ):
            return None  # Negative-weight cycle detected

    return distances


vertices = ["A", "B", "C"]
edges = [
    ("A", "B", 4),
    ("A", "C", 5),
    ("B", "C", -2)
]

print(bellman_ford(vertices, edges, "A"))
# {'A': 0, 'B': 4, 'C': 2}
```

Returning `None` here indicates a reachable negative-weight cycle. A more detailed implementation could return a separate status and distance map.

### Complexity

- Time: O(VE)
- Space: O(V)

## 4. Topological Sorting

Topological sorting produces an ordering of vertices in a **directed acyclic graph (DAG)** such that every directed edge goes from an earlier vertex to a later vertex.

It is useful for:
- Task scheduling
- Course prerequisites
- Build systems
- Dependency resolution

### Kahn's Algorithm

Kahn's algorithm repeatedly processes vertices with an indegree of zero.

Indegree is the number of incoming edges to a vertex.

```python
from collections import deque


def topological_sort(graph):
    indegree = {vertex: 0 for vertex in graph}

    for vertex in graph:
        for neighbor in graph[vertex]:
            indegree[neighbor] += 1

    queue = deque(
        vertex
        for vertex in graph
        if indegree[vertex] == 0
    )

    order = []

    while queue:
        vertex = queue.popleft()
        order.append(vertex)

        for neighbor in graph[vertex]:
            indegree[neighbor] -= 1

            if indegree[neighbor] == 0:
                queue.append(neighbor)

    if len(order) != len(graph):
        raise ValueError("Graph contains a cycle")

    return order


graph = {
    "Learn Python": ["Learn DSA"],
    "Learn DSA": ["Build Projects"],
    "Build Projects": [],
    "Learn SQL": ["Build Projects"]
}

print(topological_sort(graph))
```

The precise valid ordering can vary depending on the order in which vertices are processed.

### Complexity

- Time: O(V + E)
- Space: O(V)

A directed graph with a cycle has no topological ordering.

## 5. Minimum Spanning Tree (MST)

A **minimum spanning tree** of a connected, undirected, weighted graph is a set of edges that connects all vertices without cycles and has the minimum possible total edge weight.

For V vertices, a spanning tree contains exactly V - 1 edges.

Two major MST algorithms are Kruskal's and Prim's algorithms.

## 6. Union-Find (Disjoint Set Union)

Union-Find maintains groups of connected elements.

Its main operations are:

- `find(x)`: Find the representative of the set containing x.
- `union(a, b)`: Merge the sets containing a and b.

Path compression and union by rank or size make these operations very efficient.

```python
class UnionFind:
    def __init__(self, n):
        self.parent = list(range(n))
        self.size = [1] * n

    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])

        return self.parent[x]

    def union(self, a, b):
        root_a = self.find(a)
        root_b = self.find(b)

        if root_a == root_b:
            return False

        # Attach the smaller tree to the larger tree
        if self.size[root_a] < self.size[root_b]:
            root_a, root_b = root_b, root_a

        self.parent[root_b] = root_a
        self.size[root_a] += self.size[root_b]

        return True


uf = UnionFind(5)

uf.union(0, 1)
uf.union(1, 2)

print(uf.find(0) == uf.find(2))  # True
print(uf.find(0) == uf.find(4))  # False
```

This implementation assumes vertices are represented by integers from `0` to `n - 1`.

## 7. Kruskal's Algorithm

Kruskal's algorithm builds an MST by processing edges in ascending order of weight.

It uses Union-Find to avoid adding edges that would form a cycle.

```python
def kruskal(vertices, edges):
    # Each edge is (weight, u, v)
    edges = sorted(edges)
    uf = UnionFind(vertices)

    mst = []
    total_weight = 0

    for weight, u, v in edges:
        if uf.union(u, v):
            mst.append((u, v, weight))
            total_weight += weight

            if len(mst) == vertices - 1:
                break

    if len(mst) != vertices - 1:
        raise ValueError("Graph is disconnected")

    return mst, total_weight


edges = [
    (1, 0, 1),
    (3, 0, 2),
    (2, 1, 2),
    (4, 1, 3),
    (5, 2, 3)
]

mst, total = kruskal(4, edges)

print(mst)
print(total)
```

### Complexity

- Sorting edges: O(E log E)
- Union-Find operations: nearly constant amortized time per operation
- Overall: O(E log E)

## 8. Prim's Algorithm

Prim's algorithm grows an MST from a starting vertex.

At each step, it chooses the lowest-weight edge connecting a visited vertex to an unvisited vertex.

```python
import heapq


def prim(graph, start):
    visited = set()
    heap = [(0, start, None)]
    mst = []
    total_weight = 0

    while heap and len(visited) < len(graph):
        weight, vertex, parent = heapq.heappop(heap)

        if vertex in visited:
            continue

        visited.add(vertex)
        total_weight += weight

        if parent is not None:
            mst.append((parent, vertex, weight))

        for neighbor, edge_weight in graph[vertex]:
            if neighbor not in visited:
                heapq.heappush(
                    heap,
                    (edge_weight, neighbor, vertex)
                )

    if len(visited) != len(graph):
        raise ValueError("Graph is disconnected")

    return mst, total_weight


graph = {
    0: [(1, 1), (2, 3)],
    1: [(0, 1), (2, 2), (3, 4)],
    2: [(0, 3), (1, 2), (3, 5)],
    3: [(1, 4), (2, 5)]
}

mst, total = prim(graph, 0)

print(mst)
print(total)
```

This implementation assumes an undirected graph represented with edges in both directions. The heap tuples also assume vertex labels can be compared if weights tie.

### Complexity

With an adjacency list and a binary heap, the usual complexity is O(E log E) for this implementation.

## 9. Comparing the Algorithms

| Algorithm | Main purpose | Handles negative edges? |
|---|---|---|
| Dijkstra | Single-source shortest paths | No |
| Bellman-Ford | Single-source shortest paths | Yes |
| Topological sort | Order DAG dependencies | Not applicable |
| Kruskal | Minimum spanning tree | Edge weights may be negative |
| Prim | Minimum spanning tree | Edge weights may be negative |
| Union-Find | Track disjoint groups | Not a shortest-path algorithm |

## 10. Practice Problems

1. Implement Dijkstra's algorithm.
2. Detect a reachable negative-weight cycle using Bellman-Ford.
3. Find a valid topological ordering of a DAG.
4. Detect a cycle in a directed graph.
5. Implement Union-Find with path compression.
6. Implement Kruskal's algorithm.
7. Implement Prim's algorithm.
8. Compare Dijkstra and Bellman-Ford on the same graph.
9. Determine whether a disconnected graph has a spanning tree.
10. Find the minimum cost to connect all vertices.

## Key Takeaways

- Dijkstra requires non-negative edge weights.
- Bellman-Ford supports negative edges and detects reachable negative cycles.
- Topological sorting applies to directed acyclic graphs.
- Kruskal and Prim find minimum spanning trees.
- Union-Find helps track components and prevent cycles in Kruskal's algorithm.
