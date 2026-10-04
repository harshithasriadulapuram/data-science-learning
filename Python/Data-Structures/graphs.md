
# Graphs in Python

## 1. What Is a Graph?

A **graph** is a non-linear data structure consisting of:

- **Vertices (nodes):** The entities in the graph.
- **Edges:** The connections between vertices.

Graphs represent relationships between objects.

Examples:
- Social networks
- Road maps
- Computer networks
- Flight routes
- Dependencies between tasks

Example:

```text
    A ----- B
    |       |
    |       |
    C ----- D
```

Here, A, B, C, and D are vertices, and the lines represent edges.

## 2. Types of Graphs

### Undirected Graph

Edges have no direction.

If A is connected to B, you can travel between them in either direction.

```text
A ----- B
```

### Directed Graph

Edges have a direction.

```text
A ----> B
```

This means there is an edge from A to B, but not necessarily from B to A.

### Weighted Graph

Each edge has a weight, such as distance, cost, or time.

```text
A --5-- B
B --3-- C
```

### Unweighted Graph

Edges represent connections without numerical weights.

### Connected Graph

In an undirected graph, every vertex can be reached from every other vertex.

### Cyclic and Acyclic Graphs

- A cyclic graph contains at least one cycle.
- An acyclic graph contains no cycles.
- A directed acyclic graph is called a DAG.

## 3. Graph Representation

Graphs can be represented using adjacency lists or adjacency matrices.

### Adjacency List

An adjacency list stores the neighbors of each vertex.

```python
graph = {
    "A": ["B", "C"],
    "B": ["A", "D"],
    "C": ["A", "D"],
    "D": ["B", "C"]
}

print(graph["A"])  # ['B', 'C']
```

For an undirected graph, each edge is recorded in both directions.

For example, the edge A–B appears in both `graph["A"]` and `graph["B"]`.

### Adjacency Matrix

An adjacency matrix uses a two-dimensional array.

```python
# Vertices: A, B, C

matrix = [
    [0, 1, 1],
    [1, 0, 0],
    [1, 0, 0]
]

# A is connected to B
print(matrix[0][1])  # 1
```

Here, `1` indicates an edge and `0` indicates no edge.

For a weighted graph, matrix entries can store weights instead.

### Comparing Representations

| Feature | Adjacency List | Adjacency Matrix |
|---|---|---|
| Space | O(V + E) | O(V²) |
| Check whether an edge exists | O(degree of vertex) in a typical list | O(1) |
| Iterate through neighbors | O(degree of vertex) | O(V) |
| Best suited for | Sparse graphs | Dense graphs |

Here, V is the number of vertices and E is the number of edges.

## 4. Creating a Graph Using Python

We can build an undirected graph with an adjacency list.

```python
def add_edge(graph, u, v):
    graph.setdefault(u, [])
    graph.setdefault(v, [])

    graph[u].append(v)
    graph[v].append(u)


graph = {}

add_edge(graph, "A", "B")
add_edge(graph, "A", "C")
add_edge(graph, "B", "D")

print(graph)
```

The `setdefault()` calls ensure that both vertices exist in the adjacency list.

This implementation assumes each edge is added only once. If duplicate edges are possible, add a check to avoid duplicates.

## 5. Breadth-First Search (BFS)

**Breadth-First Search** explores a graph level by level.

It uses a queue to process the earliest discovered vertices first.

BFS is useful for:
- Finding the shortest path in an unweighted graph.
- Exploring connected components.
- Finding vertices within a certain number of edges.

### BFS Implementation

```python
from collections import deque


def bfs(graph, start):
    visited = {start}
    queue = deque([start])

    while queue:
        vertex = queue.popleft()
        print(vertex, end=" ")

        for neighbor in graph.get(vertex, []):
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)


graph = {
    "A": ["B", "C"],
    "B": ["A", "D"],
    "C": ["A", "D"],
    "D": ["B", "C"]
}

bfs(graph, "A")
# A B C D
```

### How BFS Works

1. Start at vertex A.
2. Mark A as visited and add it to the queue.
3. Remove A from the queue.
4. Visit A's unvisited neighbors and add them to the queue.
5. Continue until the queue is empty.

Marking a vertex as visited when it is added to the queue prevents it from being added repeatedly.

## 6. Depth-First Search (DFS)

**Depth-First Search** explores as far as possible along one path before backtracking.

It can be implemented using recursion or an explicit stack.

### Recursive DFS

```python
def dfs(graph, vertex, visited=None):
    if visited is None:
        visited = set()

    if vertex in visited:
        return

    visited.add(vertex)
    print(vertex, end=" ")

    for neighbor in graph.get(vertex, []):
        dfs(graph, neighbor, visited)


graph = {
    "A": ["B", "C"],
    "B": ["A", "D"],
    "C": ["A", "D"],
    "D": ["B", "C"]
}

dfs(graph, "A")
# A B D C
```

The exact traversal order depends on the order of neighbors in the adjacency list.

### Iterative DFS

```python
def dfs_iterative(graph, start):
    visited = set()
    stack = [start]

    while stack:
        vertex = stack.pop()

        if vertex in visited:
            continue

        visited.add(vertex)
        print(vertex, end=" ")

        for neighbor in reversed(graph.get(vertex, [])):
            if neighbor not in visited:
                stack.append(neighbor)
```

This implementation uses an explicit stack instead of recursive calls.

## 7. BFS vs DFS

| Feature | BFS | DFS |
|---|---|---|
| Main structure | Queue | Stack or recursion |
| Exploration | Level by level | Deep along a path |
| Shortest path in an unweighted graph | Yes | Not guaranteed |
| Common applications | Minimum edge distance, levels | Cycle detection, traversal, backtracking |
| Time complexity | O(V + E) | O(V + E) |

These time complexities assume an adjacency-list representation and a traversal that processes each vertex and edge a bounded number of times.

## 8. Detecting a Cycle in an Undirected Graph

A cycle exists when a path leads back to an already visited vertex through an edge that is not simply the edge to the current vertex's parent.

```python
def has_cycle(graph):
    visited = set()

    def dfs(vertex, parent):
        visited.add(vertex)

        for neighbor in graph.get(vertex, []):
            if neighbor not in visited:
                if dfs(neighbor, vertex):
                    return True
            elif neighbor != parent:
                return True

        return False

    for vertex in graph:
        if vertex not in visited:
            if dfs(vertex, None):
                return True

    return False
```

This implementation assumes an undirected adjacency list with consistent edges in both directions and no duplicate edges or self-loops.

## 9. Finding Connected Components

A connected component is a maximal group of vertices connected to one another.

```python
def connected_components(graph):
    visited = set()
    components = []

    for vertex in graph:
        if vertex in visited:
            continue

        component = []
        stack = [vertex]
        visited.add(vertex)

        while stack:
            current = stack.pop()
            component.append(current)

            for neighbor in graph.get(current, []):
                if neighbor not in visited:
                    visited.add(neighbor)
                    stack.append(neighbor)

        components.append(component)

    return components


graph = {
    1: [2],
    2: [1],
    3: [4],
    4: [3],
    5: []
}

print(connected_components(graph))
# [[1, 2], [3, 4], [5]]
```

This implementation treats the graph as undirected.

## 10. Shortest Path in an Unweighted Graph

BFS can find a shortest path measured by the number of edges.

```python
from collections import deque


def shortest_path(graph, start, target):
    queue = deque([start])
    parent = {start: None}

    while queue:
        vertex = queue.popleft()

        if vertex == target:
            break

        for neighbor in graph.get(vertex, []):
            if neighbor not in parent:
                parent[neighbor] = vertex
                queue.append(neighbor)

    if target not in parent:
        return None

    path = []
    current = target

    while current is not None:
        path.append(current)
        current = parent[current]

    return path[::-1]
```

Example:

```python
graph = {
    "A": ["B", "C"],
    "B": ["A", "D"],
    "C": ["A", "D"],
    "D": ["B", "C"]
}

print(shortest_path(graph, "A", "D"))
# ['A', 'B', 'D']
```

If multiple shortest paths exist, the returned path depends on neighbor order.

## 11. Important Graph Algorithms to Learn Next

- **Dijkstra's algorithm:** Shortest paths from one source in graphs with non-negative edge weights.
- **Bellman-Ford algorithm:** Shortest paths with support for negative edge weights and detection of reachable negative-weight cycles.
- **Topological sorting:** Ordering vertices in a directed acyclic graph.
- **Kruskal's algorithm:** Minimum spanning tree using edges in increasing weight order.
- **Prim's algorithm:** Minimum spanning tree grown from a starting vertex.
- **Union-Find:** Efficiently tracks connected groups and helps detect cycles in suitable graph algorithms.

## 12. Common Interview Questions

1. What is a graph?
2. What is the difference between directed and undirected graphs?
3. What is the difference between an adjacency list and an adjacency matrix?
4. Explain BFS and DFS.
5. Why does BFS find a shortest path in an unweighted graph?
6. How can you detect a cycle in an undirected graph?
7. What are connected components?
8. What is a DAG?
9. What is topological sorting?
10. How does Dijkstra's algorithm work?
11. What is a minimum spanning tree?
12. What is the time complexity of BFS and DFS?

## 13. Practice Problems

Solve these independently:

1. Represent an undirected graph using an adjacency list.
2. Represent a graph using an adjacency matrix.
3. Implement BFS.
4. Implement recursive DFS.
5. Implement iterative DFS.
6. Count connected components.
7. Detect a cycle in an undirected graph.
8. Find the shortest path in an unweighted graph.
9. Determine whether a path exists between two vertices.
10. Implement topological sorting.
11. Implement Dijkstra's algorithm.
12. Find a minimum spanning tree.

## Key Takeaways

- Graphs model relationships between vertices.
- Adjacency lists are space-efficient for sparse graphs.
- BFS uses a queue and explores level by level.
- DFS uses a stack or recursion and explores deeply before backtracking.
- BFS finds shortest paths in unweighted graphs.
- Graph algorithms commonly run in O(V + E) time when using adjacency lists.
