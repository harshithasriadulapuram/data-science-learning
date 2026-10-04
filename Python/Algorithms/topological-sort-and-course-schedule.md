
# Topological Sort and Course Schedule

This file covers topological sorting, dependency ordering, cycle detection, and the Course Schedule interview problem.

## 1. What Is Topological Sorting?

Topological sorting arranges the vertices of a directed graph in an order such that, for every edge `u -> v`, vertex `u` appears before vertex `v`.

It is possible only when the graph is a Directed Acyclic Graph (DAG).

Common applications:
- Course prerequisite planning
- Build systems and task scheduling
- Package dependency resolution
- Workflow execution

A DAG can have multiple valid topological orders.

## 2. Topological Sort Using Kahn's Algorithm (BFS)

Kahn's algorithm repeatedly processes vertices whose indegree is zero.

**Indegree:** The number of incoming edges to a vertex.

Steps:
1. Calculate the indegree of every vertex.
2. Add all zero-indegree vertices to a queue.
3. Remove a vertex, add it to the result, and reduce the indegrees of its neighbors.
4. Add any neighbor whose indegree becomes zero.
5. If every vertex is processed, the result is a valid topological order. Otherwise, the graph contains a cycle.

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
        return None

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

One valid output is:

```text
['A', 'B', 'C', 'D', 'E', 'F']
```

Other valid orders are possible.

**Complexity:**
- Time: `O(V + E)`
- Space: `O(V)`

## 3. Topological Sort Using DFS

DFS-based topological sorting adds each vertex to a list after exploring all its outgoing neighbors. Reversing that list gives a topological order.

A three-state approach also detects cycles.

```python
def topological_sort_dfs(graph):
    nodes = set(graph)

    for neighbors in graph.values():
        nodes.update(neighbors)

    # 0 = unvisited, 1 = visiting, 2 = completed
    state = {}
    order = []

    def dfs(node):
        state[node] = 1

        for neighbor in graph.get(node, []):
            if state.get(neighbor, 0) == 1:
                return False

            if state.get(neighbor, 0) == 0:
                if not dfs(neighbor):
                    return False

        state[node] = 2
        order.append(node)
        return True

    for node in nodes:
        if state.get(node, 0) == 0:
            if not dfs(node):
                return None

    return order[::-1]
```

**Complexity:**
- Time: `O(V + E)`
- Space: `O(V)` including the recursion stack.

For very deep graphs, an iterative algorithm avoids Python's recursion-depth limit.

## 4. Course Schedule — Can All Courses Be Completed?

**Problem:** There are `num_courses` courses numbered from `0` to `num_courses - 1`. Each prerequisite pair `[a, b]` means you must complete course `b` before taking course `a`.

Return `True` if all courses can be completed; otherwise, return `False`.

Model each prerequisite as a directed edge:

`b -> a`

If the graph contains a directed cycle, the courses cannot all be completed.

```python
from collections import deque

def can_finish(num_courses, prerequisites):
    graph = [[] for _ in range(num_courses)]
    indegree = [0] * num_courses

    for course, prerequisite in prerequisites:
        graph[prerequisite].append(course)
        indegree[course] += 1

    queue = deque(
        course
        for course in range(num_courses)
        if indegree[course] == 0
    )

    completed = 0

    while queue:
        course = queue.popleft()
        completed += 1

        for next_course in graph[course]:
            indegree[next_course] -= 1

            if indegree[next_course] == 0:
                queue.append(next_course)

    return completed == num_courses
```

Example:

```python
print(can_finish(2, [[1, 0]]))  # True
print(can_finish(2, [[1, 0], [0, 1]]))  # False
print(can_finish(3, []))  # True
```

Explanation:
- In the first example, course `0` can be completed before course `1`.
- In the second example, each course depends on the other, creating a cycle.
- In the third example, there are no prerequisites.

**Complexity:**
- Time: `O(V + E)`
- Space: `O(V + E)`

## 5. Course Schedule II — Return a Valid Course Order

**Problem:** Return one valid order in which all courses can be completed. If no valid order exists, return an empty list.

```python
from collections import deque

def find_course_order(num_courses, prerequisites):
    graph = [[] for _ in range(num_courses)]
    indegree = [0] * num_courses

    for course, prerequisite in prerequisites:
        graph[prerequisite].append(course)
        indegree[course] += 1

    queue = deque(
        course
        for course in range(num_courses)
        if indegree[course] == 0
    )

    order = []

    while queue:
        course = queue.popleft()
        order.append(course)

        for next_course in graph[course]:
            indegree[next_course] -= 1

            if indegree[next_course] == 0:
                queue.append(next_course)

    return order if len(order) == num_courses else []
```

Example:

```python
print(find_course_order(4, [[1, 0], [2, 0], [3, 1], [3, 2]]))
```

One valid output is:

```text
[0, 1, 2, 3]
```

The relative order of courses `1` and `2` can vary.

**Complexity:**
- Time: `O(V + E)`
- Space: `O(V + E)`

## 6. Topological Sort vs. BFS vs. DFS

| Algorithm | Purpose |
|---|---|
| BFS | Explore a graph level by level |
| DFS | Explore paths deeply before backtracking |
| Topological sort | Order vertices according to dependencies |
| Kahn's algorithm | Topological sorting using indegrees and a queue |
| DFS-based topological sort | Topological sorting using finishing times |

BFS and DFS are general traversal strategies. Topological sorting is a specific ordering task that can be implemented using either approach.

## Interview Revision Checklist

- [ ] Define a DAG and topological ordering.
- [ ] Explain indegree.
- [ ] Implement Kahn's algorithm.
- [ ] Implement DFS-based topological sorting.
- [ ] Detect a directed cycle.
- [ ] Solve Course Schedule.
- [ ] Solve Course Schedule II.
- [ ] Explain why a directed cycle prevents a valid topological order.
- [ ] State the time and space complexity.

## Practice Strategy

1. Draw a small directed graph.
2. Calculate each vertex's indegree.
3. Trace Kahn's algorithm by hand.
4. Test a graph with a cycle and a graph without a cycle.
5. Explain how prerequisite relationships become directed edges.
