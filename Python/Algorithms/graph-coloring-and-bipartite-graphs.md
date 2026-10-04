
# Graph Coloring and Bipartite Graphs

This file covers graph coloring, bipartite graph detection, and common graph interview problems using Python.

## 1. What Is Graph Coloring?

Graph coloring assigns colors to vertices so that adjacent vertices have different colors.

Applications include:
- Exam and timetable scheduling
- Register allocation in compilers
- Frequency assignment
- Resource allocation

The **chromatic number** is the minimum number of colors required to color a graph properly.

## 2. Check Whether a Graph Is Bipartite

A graph is bipartite if its vertices can be divided into two groups such that every edge connects vertices from different groups.

A graph is bipartite if and only if it contains no odd-length cycle.

We can test this by coloring vertices with two colors using BFS.

```python
from collections import deque

def is_bipartite(graph):
    color = {}

    for start in graph:
        if start in color:
            continue

        color[start] = 0
        queue = deque([start])

        while queue:
            node = queue.popleft()

            for neighbor in graph[node]:
                if neighbor not in color:
                    color[neighbor] = 1 - color[node]
                    queue.append(neighbor)
                elif color[neighbor] == color[node]:
                    return False

    return True
```

Example:

```python
graph1 = {
    0: [1, 3],
    1: [0, 2],
    2: [1, 3],
    3: [0, 2]
}

print(is_bipartite(graph1))  # True
```

This graph is a cycle of length four, so it is bipartite.

```python
graph2 = {
    0: [1, 2],
    1: [0, 2],
    2: [0, 1]
}

print(is_bipartite(graph2))  # False
```

This graph is a triangle, which contains an odd-length cycle.

**Complexity:**
- Time: `O(V + E)`
- Space: `O(V)`

The graph should be represented with adjacency lists, including isolated vertices as keys.

## 3. Bipartite Checking Using DFS

The same idea can be implemented using depth-first search.

```python
def is_bipartite_dfs(graph):
    color = {}

    def dfs(node, current_color):
        color[node] = current_color

        for neighbor in graph[node]:
            if neighbor not in color:
                if not dfs(neighbor, 1 - current_color):
                    return False
            elif color[neighbor] == current_color:
                return False

        return True

    for node in graph:
        if node not in color:
            if not dfs(node, 0):
                return False

    return True
```

**Complexity:**
- Time: `O(V + E)`
- Space: `O(V)` including the recursion stack.

For very deep graphs, iterative DFS avoids Python's recursion-depth limit.

## 4. M-Coloring Problem

**Problem:** Determine whether a graph can be colored using at most `m` colors so that no two adjacent vertices share a color.

A standard solution uses backtracking.

```python
def can_color_graph(graph, m):
    n = len(graph)
    colors = [0] * n

    def is_safe(node, color):
        for neighbor in graph[node]:
            if colors[neighbor] == color:
                return False
        return True

    def backtrack(node):
        if node == n:
            return True

        for color in range(1, m + 1):
            if is_safe(node, color):
                colors[node] = color

                if backtrack(node + 1):
                    return True

                colors[node] = 0

        return False

    return backtrack(0)
```

Example:

```python
triangle = [
    [1, 2],
    [0, 2],
    [0, 1]
]

print(can_color_graph(triangle, 2))  # False
print(can_color_graph(triangle, 3))  # True
```

The triangle requires three colors because every vertex is adjacent to the other two.

**Complexity:**
- Time: `O(m^V * (V + E))` as a loose upper bound for this implementation.
- Space: `O(V)` for the colors and recursion stack.

Graph coloring is computationally difficult in general; backtracking may take exponential time.

## 5. Find All Connected Components

Connected components are maximal groups of vertices where each vertex can reach the others.

```python
def connected_components(graph):
    visited = set()
    components = []

    for start in graph:
        if start in visited:
            continue

        component = []
        stack = [start]
        visited.add(start)

        while stack:
            node = stack.pop()
            component.append(node)

            for neighbor in graph[node]:
                if neighbor not in visited:
                    visited.add(neighbor)
                    stack.append(neighbor)

        components.append(component)

    return components
```

Example:

```python
graph = {
    0: [1],
    1: [0],
    2: [3],
    3: [2],
    4: []
}

print(connected_components(graph))
# [[0, 1], [2, 3], [4]]
```

The order of components and nodes may vary depending on traversal order.

**Complexity:**
- Time: `O(V + E)`
- Space: `O(V)`

## 6. Important Interview Questions

1. What is graph coloring?
2. What is a bipartite graph?
3. Why can a triangle not be colored with two colors?
4. How can BFS detect whether a graph is bipartite?
5. What is the relationship between odd cycles and bipartite graphs?
6. What is the difference between graph coloring and the M-coloring problem?
7. What is backtracking, and why can graph coloring require exponential time?
8. How do you find connected components in an undirected graph?

## Revision Checklist

- [ ] Explain vertex coloring and chromatic number.
- [ ] Detect a bipartite graph using BFS.
- [ ] Detect a bipartite graph using DFS.
- [ ] Solve the M-coloring problem using backtracking.
- [ ] Find connected components.
- [ ] Explain the time and space complexity of each solution.
