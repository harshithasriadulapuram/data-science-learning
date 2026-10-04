
# Disjoint Set Union (DSU) / Union-Find in Python

## 1. What Is Disjoint Set Union?

**Disjoint Set Union (DSU)**, also called **Union-Find**, is a data structure that keeps track of groups of elements that are disjoint, meaning no element belongs to more than one group.

It efficiently supports two main operations:

1. **Find:** Determine which group an element belongs to.
2. **Union:** Merge the groups containing two elements.

### Example

Initially, each element belongs to its own group:

```text
{0}  {1}  {2}  {3}  {4}
```

After `union(0, 1)`:

```text
{0, 1}  {2}  {3}  {4}
```

After `union(1, 2)`:

```text
{0, 1, 2}  {3}  {4}
```

Now, elements `0`, `1`, and `2` belong to the same group.

## 2. Why Do We Use DSU?

DSU is useful when we need to maintain connected groups while relationships are added.

Common applications include:

- Detecting cycles in an undirected graph
- Finding connected components
- Kruskal's Minimum Spanning Tree algorithm
- Network connectivity
- Grouping related elements
- Combining sets efficiently

## 3. Important Concepts

### Parent

Each element stores a parent pointer.

Initially, each element is its own parent.

```python
parent = [0, 1, 2, 3, 4]
```

This means:

- Parent of `0` is `0`.
- Parent of `1` is `1`.
- Parent of `2` is `2`.

An element that is its own parent is the representative, or root, of its set.

### Find

The `find(x)` operation follows parent pointers until it reaches the root of the set containing `x`.

If two elements have the same root, they belong to the same set.

### Union

The `union(a, b)` operation merges the sets containing `a` and `b`.

If both elements already belong to the same set, no merge is necessary.

## 4. Basic DSU Implementation

Let's implement DSU without optimizations first.

```python
class DSU:
    def __init__(self, n):
        self.parent = list(range(n))

    def find(self, x):
        if self.parent[x] == x:
            return x

        return self.find(self.parent[x])

    def union(self, a, b):
        root_a = self.find(a)
        root_b = self.find(b)

        if root_a == root_b:
            return False

        self.parent[root_b] = root_a
        return True


dsu = DSU(5)

print(dsu.find(0))
print(dsu.find(1))

dsu.union(0, 1)

print(dsu.find(0) == dsu.find(1))  # True
```

### How It Works

1. Create one separate set for every element.
2. Find the roots of the two elements.
3. If their roots match, they are already connected.
4. Otherwise, make one root a child of the other.

This version is easy to understand, but its trees can become tall. We can improve it using path compression and union by size.

## 5. Path Compression

**Path compression** makes future `find()` operations faster by connecting visited nodes directly to the root.

Consider this parent chain:

```text
0 -> 1 -> 2 -> 3
```

If `3` is the root, finding the root of `0` visits multiple nodes.

After path compression:

```text
0 ----\
1 -----+--> 3
2 ----/
```

The nodes visited during the search point directly to the root.

### Implementation

```python
def find(self, x):
    if self.parent[x] != x:
        self.parent[x] = self.find(self.parent[x])

    return self.parent[x]
```

The assignment to `self.parent[x]` is the key step. It stores the root found by the recursive call.

## 6. Union by Size

**Union by size** attaches the smaller set's root to the larger set's root.

Maintain a `size` array that records the number of elements in each root's set.

When two sets merge:
- Compare their sizes.
- Attach the smaller set to the larger set.
- Add the smaller size to the larger root's size.

This helps keep the trees shallow.

## 7. Complete Optimized DSU Implementation

This implementation combines path compression and union by size.

```python
class DSU:
    def __init__(self, n):
        self.parent = list(range(n))
        self.size = [1] * n
        self.components = n

    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])

        return self.parent[x]

    def union(self, a, b):
        root_a = self.find(a)
        root_b = self.find(b)

        if root_a == root_b:
            return False

        # Attach the smaller set to the larger set.
        if self.size[root_a] < self.size[root_b]:
            root_a, root_b = root_b, root_a

        self.parent[root_b] = root_a
        self.size[root_a] += self.size[root_b]
        self.components -= 1

        return True

    def connected(self, a, b):
        return self.find(a) == self.find(b)

    def component_size(self, x):
        return self.size[self.find(x)]

    def count_components(self):
        return self.components


dsu = DSU(5)

dsu.union(0, 1)
dsu.union(1, 2)
dsu.union(3, 4)

print(dsu.connected(0, 2))       # True
print(dsu.connected(0, 4))       # False
print(dsu.component_size(1))     # 3
print(dsu.count_components())    # 2
```

### Understanding the Additional Methods

- `connected(a, b)`: Checks whether two elements belong to the same set.
- `component_size(x)`: Returns the number of elements in the set containing `x`.
- `count_components()`: Returns the number of distinct sets.

The `components` counter decreases only when two different sets are successfully merged.

## 8. Understanding the Example Step by Step

Initially, there are five components:

```text
{0} {1} {2} {3} {4}
```

After `union(0, 1)`:

```text
{0, 1} {2} {3} {4}
```

There are four components.

After `union(1, 2)`:

```text
{0, 1, 2} {3} {4}
```

There are three components.

After `union(3, 4)`:

```text
{0, 1, 2} {3, 4}
```

There are two components.

Calling `union(0, 2)` now returns `False`, because `0` and `2` already belong to the same component.

The number of components remains two.

## 9. Time and Space Complexity

Let `n` be the number of elements and `m` the number of DSU operations.

| Operation | Complexity |
|---|---|
| Initialize DSU | O(n) |
| Find without optimizations | O(n) worst case |
| Union without optimizations | O(n) worst case |
| Find with both optimizations | O(α(n)) amortized |
| Union with both optimizations | O(α(n)) amortized |
| Total for m operations | O(n + m α(n)) |
| Space | O(n) |

Here, `α(n)` is the inverse Ackermann function, which grows extremely slowly. For practical input sizes, it is effectively a very small constant.

The optimized bounds apply to a sequence of operations, amortized across that sequence.

## 10. DSU for Cycle Detection in an Undirected Graph

An undirected graph contains a cycle if an edge connects two vertices that are already connected by previously processed edges.

Using DSU, we can detect this by checking whether the edge's endpoints have the same representative.

```python
def has_cycle(n, edges):
    dsu = DSU(n)

    for u, v in edges:
        if not dsu.union(u, v):
            return True

    return False


edges1 = [(0, 1), (1, 2), (2, 3)]
edges2 = [(0, 1), (1, 2), (2, 0)]

print(has_cycle(4, edges1))  # False
print(has_cycle(3, edges2))  # True
```

The first graph has no cycle. The second contains a cycle because its final edge joins vertices already in the same component.

This technique assumes the graph is undirected and that the edge list is processed as given. Self-loops and repeated edges also count as cycles under the usual undirected graph definition.

## 11. DSU and Kruskal's Algorithm

Kruskal's algorithm finds a Minimum Spanning Tree (MST) for a connected, weighted, undirected graph.

Its basic steps are:

1. Sort edges by increasing weight.
2. Start with each vertex in its own component.
3. Process edges from the smallest weight upward.
4. Add an edge if its endpoints are in different components.
5. Merge those components using DSU.
6. Stop after selecting `n - 1` edges for a connected graph with `n` vertices.

DSU helps Kruskal's algorithm efficiently determine whether adding an edge would create a cycle.

For a disconnected graph, the same process produces a minimum spanning forest rather than one spanning tree.

## 12. DSU vs Other Data Structures

| Feature | DSU | Graph Adjacency List |
|---|---|---|
| Main purpose | Maintain disjoint sets | Represent graph edges |
| Check connectivity | Very efficient for added connections | Usually requires graph traversal |
| Add an undirected connection | Union two sets | Add the edge to the representation |
| Find a path | Not supported | Possible with graph algorithms |
| Retrieve all graph neighbors | Not supported directly | Supported |

DSU is not a replacement for every graph data structure. It is best when the main task is merging groups and checking connectivity.

## 13. Common Interview Questions

1. What is Disjoint Set Union?
2. What are the `find()` and `union()` operations?
3. What is path compression?
4. What is union by size?
5. What is union by rank?
6. Why is the optimized DSU almost constant time per operation?
7. How can DSU detect a cycle in an undirected graph?
8. How does DSU help in Kruskal's algorithm?
9. How can you count connected components using DSU?
10. Can DSU efficiently answer shortest-path queries?

The answer to the last question is no. DSU tracks connectivity, not shortest paths.

## 14. Practice Problems

### Beginner
1. Implement `find()` and `union()`.
2. Check whether two elements are connected.
3. Count the number of components.
4. Return the size of a component.

### Intermediate
5. Detect a cycle in an undirected graph.
6. Count connected components from an edge list.
7. Implement union by rank.
8. Compare DSU with graph traversal for connectivity.

### Advanced
9. Implement Kruskal's Minimum Spanning Tree algorithm.
10. Solve a dynamic connectivity problem.
11. Group accounts or users that share identifiers.
12. Solve the Number of Provinces problem using DSU.

## Key Takeaways

- DSU maintains disjoint groups of elements.
- `find()` identifies the representative of a set.
- `union()` merges two different sets.
- Path compression speeds up future searches.
- Union by size or rank helps keep trees shallow.
- DSU is widely used in connectivity problems, cycle detection, and Kruskal's algorithm.
