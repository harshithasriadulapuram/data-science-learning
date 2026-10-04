
# Graph Interview Practice in Python

This file covers common graph interview problems with Python implementations, examples, and time and space complexity.

## 1. Graph Representation Using an Adjacency List

A graph contains vertices (nodes) and edges (connections). An adjacency list stores each vertex's neighbors.

```python
graph = {
    0: [1, 2],
    1: [0, 3],
    2: [0, 3],
    3: [1, 2]
}
```

This represents an undirected graph.

To add an undirected edge:

```python
def add_edge(graph, u, v):
    graph.setdefault(u, []).append(v)
    graph.setdefault(v, []).append(u)

add_edge(graph, 0, 3)
```

For an undirected graph, each edge is stored in both directions.

---

## 2. Breadth-First Search (BFS)

**Problem:** Visit graph nodes level by level, starting from a given node.

BFS uses a queue and is useful for finding shortest paths in unweighted graphs.

```python
from collections import deque

def bfs(graph, start):
    visited = {start}
    queue = deque([start])
    order = []

    while queue:
        node = queue.popleft()
        order.append(node)

        for neighbor in graph.get(node, []):
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)

    return order
```

Example:

```python
graph = {
    0: [1, 2],
    1: [0, 3],
    2: [0, 3],
    3: [1, 2]
}

print(bfs(graph, 0))
# [0, 1, 2, 3]
```

**Complexity:**
- Time: `O(V + E)`
- Space: `O(V)`

Here, `V` is the number of vertices and `E` is the number of edges.

---

## 3. Depth-First Search (DFS)

**Problem:** Explore a graph by going as deep as possible before backtracking.

This implementation uses an explicit stack.

```python
def dfs(graph, start):
    visited = set()
    stack = [start]
    order = []

    while stack:
        node = stack.pop()

        if node in visited:
            continue

        visited.add(node)
        order.append(node)

        for neighbor in reversed(graph.get(node, [])):
            if neighbor not in visited:
                stack.append(neighbor)

    return order
```

Example:

```python
print(dfs(graph, 0))
# [0, 1, 3, 2]
```

The exact traversal order depends on the order of neighbors.

**Complexity:**
- Time: `O(V + E)`
- Space: `O(V)`

---

## 4. Detect a Cycle in an Undirected Graph

**Problem:** Determine whether an undirected graph contains a cycle.

Use DFS and track the parent of each node. If a visited neighbor is not the parent, a cycle exists.

```python
def has_cycle_undirected(graph):
    visited = set()

    def dfs(node, parent):
        visited.add(node)

        for neighbor in graph.get(node, []):
            if neighbor not in visited:
                if dfs(neighbor, node):
                    return True
            elif neighbor != parent:
                return True

        return False

    for node in graph:
        if node not in visited:
            if dfs(node, None):
                return True

    return False
```

Example:

```python
tree_graph = {
    0: [1, 2],
    1: [0],
    2: [0]
}

print(has_cycle_undirected(tree_graph))  # False
print(has_cycle_undirected(graph))       # True
```

This recursive version assumes the graph is a simple undirected graph without parallel edges or self-loops.

**Complexity:**
- Time: `O(V + E)`
- Space: `O(V)` including the recursion stack.

---

## 5. Detect a Cycle in a Directed Graph

**Problem:** Determine whether a directed graph contains a cycle.

Use three states:
- `0`: Not visited
- `1`: Currently being explored
- `2`: Fully explored

Finding an edge to a node in state `1` indicates a cycle.

```python
def has_cycle_directed(graph):
    state = {}

    def dfs(node):
        state[node] = 1

        for neighbor in graph.get(node, []):
            if state.get(neighbor, 0) == 1:
                return True

            if state.get(neighbor, 0) == 0:
                if dfs(neighbor):
                    return True

        state[node] = 2
        return False

    nodes = set(graph)

    for neighbors in graph.values():
        nodes.update(neighbors)

    for node in nodes:
        if state.get(node, 0) == 0:
            if dfs(node):
                return True

    return False
```

Example:

```python
directed_graph = {
    0: [1],
    1: [2],
    2: [0]
}

print(has_cycle_directed(directed_graph))  # True
```

**Complexity:**
- Time: `O(V + E)`
- Space: `O(V)`.

---

## 6. Find the Number of Connected Components

**Problem:** Count the connected components in an undirected graph.

A connected component is a group of vertices where every vertex can reach every other vertex in that group.

```python
def count_components(graph):
    visited = set()
    components = 0

    for node in graph:
        if node not in visited:
            components += 1
            stack = [node]
            visited.add(node)

            while stack:
                current = stack.pop()

                for neighbor in graph.get(current, []):
                    if neighbor not in visited:
                        visited.add(neighbor)
                        stack.append(neighbor)

    return components
```

Example:

```python
disconnected_graph = {
    0: [1],
    1: [0],
    2: [3],
    3: [2],
    4: []
}

print(count_components(disconnected_graph))  # 3
```

**Complexity:**
- Time: `O(V + E)`
- Space: `O(V)`.

---

## 7. Shortest Path in an Unweighted Graph

**Problem:** Find the minimum number of edges from a source node to a target node.

BFS finds a shortest path when every edge has equal cost.

```python
from collections import deque

def shortest_path(graph, start, target):
    queue = deque([start])
    parent = {start: None}

    while queue:
        node = queue.popleft()

        if node == target:
            path = []

            while node is not None:
                path.append(node)
                node = parent[node]

            return path[::-1]

        for neighbor in graph.get(node, []):
            if neighbor not in parent:
                parent[neighbor] = node
                queue.append(neighbor)

    return None
```

Example:

```python
print(shortest_path(graph, 0, 3))
# [0, 1, 3]
```

If no path exists, the function returns `None`.

**Complexity:**
- Time: `O(V + E)`
- Space: `O(V)`.

---

## 8. Topological Sorting

**Problem:** Produce an ordering of vertices in a directed acyclic graph (DAG) such that every directed edge `u -> v` places `u` before `v`.

Kahn's algorithm uses indegrees and a queue.

```python
from collections import deque

def topological_sort(graph):
    nodes = set(graph)

    for neighbors in graph.values():
        nodes.update(neighbors)

    indegree = {node: 0 for node in nodes}

    for node in graph:
        for neighbor in graph[node]:
            indegree[neighbor] += 1

    queue = deque(
        node for node in nodes if indegree[node] == 0
    )
    order = []

    while queue:
        node = queue.popleft()
        order.append(node)

        for neighbor in graph.get(node, []):
            indegree[neighbor] -= 1

            if indegree[neighbor] == 0:
                queue.append(neighbor)

    if len(order) != len(nodes):
        return None  # The graph contains a cycle.

    return order
```

Example:

```python
dag = {
    "A": ["C"],
    "B": ["C", "D"],
    "C": ["E"],
    "D": ["F"],
    "E": ["F"],
    "F": []
}

print(topological_sort(dag))
```

More than one valid ordering may exist. If the graph contains a cycle, the function returns `None`.

**Complexity:**
- Time: `O(V + E)`
- Space: `O(V)`.

---

## 9. Number of Islands

**Problem:** Given a grid of `"1"` (land) and `"0"` (water), count the islands. Land cells connect horizontally or vertically, not diagonally.

```python
def num_islands(grid):
    if not grid or not grid[0]:
        return 0

    rows, cols = len(grid), len(grid[0])
    islands = 0

    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == "1":
                islands += 1
                stack = [(r, c)]
                grid[r][c] = "0"

                while stack:
                    row, col = stack.pop()

                    for dr, dc in [
                        (1, 0), (-1, 0), (0, 1), (0, -1)
                    ]:
                        nr, nc = row + dr, col + dc

                        if (
                            0 <= nr < rows
                            and 0 <= nc < cols
                            and grid[nr][nc] == "1"
                        ):
                            grid[nr][nc] = "0"
                            stack.append((nr, nc))

    return islands
```

Example:

```python
grid = [
    ["1", "1", "0", "0"],
    ["1", "0", "0", "1"],
    ["0", "0", "1", "1"]
]

print(num_islands(grid))  # 2
```

**Important:** This solution modifies the input grid by changing visited land cells to water.

**Complexity:**
- Time: `O(R * C)`
- Space: `O(R * C)` in the worst case.

Here, `R` is the number of rows and `C` is the number of columns.

---

## 10. Graph Interview Revision Checklist

- [ ] Represent a graph using an adjacency list.
- [ ] Implement BFS using a queue.
- [ ] Implement DFS using a stack or recursion.
- [ ] Detect cycles in undirected graphs.
- [ ] Detect cycles in directed graphs.
- [ ] Count connected components.
- [ ] Find shortest paths in unweighted graphs.
- [ ] Perform topological sorting on a DAG.
- [ ] Solve Number of Islands.
- [ ] Explain `O(V + E)` time complexity.

## Practice Strategy

1. Draw a small graph before writing code.
2. Decide whether BFS, DFS, or another algorithm fits the problem.
3. Track visited nodes to avoid repeated exploration.
4. Test disconnected graphs, empty inputs, and cycles.
5. Explain your approach and complexity aloud.
