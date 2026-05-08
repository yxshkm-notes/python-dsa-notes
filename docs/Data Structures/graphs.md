# Graphs

!!! abstract "What You'll Learn"
    - ✅ What a graph is and core terminology (vertex, edge, directed, weighted)
    - ✅ Adjacency list vs adjacency matrix representation
    - ✅ BFS and DFS — implementation, complexity, and when to use each
    - ✅ Cycle detection, topological sort, connected components
    - ✅ Shortest path: Dijkstra's and Bellman-Ford
    - ✅ Union-Find (Disjoint Set Union) for connectivity problems
    - ✅ Time and space complexity of every algorithm

A **graph** is a collection of **vertices** (nodes) connected by **edges**. Unlike trees, graphs have no root, no parent-child hierarchy, and can contain cycles. They model real-world networks: roads, social connections, dependencies, the internet — anything where relationships matter more than hierarchy.

!!! tip "New to Graphs?"
    Think of a graph like a **city map** — intersections are vertices, roads are edges. Some roads are one-way (directed), some have speed limits (weighted). Finding the shortest route is literally Dijkstra's algorithm running in your GPS.

!!! info "Coming from Trees?"
    A tree is just a special graph — connected, undirected, and with no cycles (acyclic). Every tree is a graph, but not every graph is a tree. All tree algorithms (DFS, BFS) apply to graphs, but graphs need extra care to handle cycles and disconnected components.

!!! warning "Keep in mind"
    The hardest part of graph problems is usually **recognising** that it's a graph problem and choosing the **right representation**. Always clarify: directed or undirected? Weighted? Can there be cycles? Is the graph connected?

---

## Core Terminology

```
Undirected Graph:          Directed Graph (Digraph):
  A --- B                    A ──► B
  |   / |                    |     |
  |  /  |                    ▼     ▼
  C --- D                    C ◄── D

Vertices (nodes): A, B, C, D
Edges: connections between vertices
Degree: number of edges on a vertex
Path: sequence of vertices connected by edges
Cycle: path that starts and ends at same vertex
Connected: every vertex reachable from every other
```

| Term | Meaning |
|---|---|
| **Vertex / Node** | A point in the graph |
| **Edge** | A connection between two vertices |
| **Directed** | Edges have direction (one-way) |
| **Undirected** | Edges go both ways |
| **Weighted** | Edges have a cost/distance |
| **Degree** | Number of edges on a vertex |
| **Path** | Sequence of vertices connected by edges |
| **Cycle** | Path that returns to its starting vertex |
| **Connected** | Every vertex reachable from every other |
| **DAG** | Directed Acyclic Graph — no cycles, used for dependencies |

---

## 1️⃣ Graph Representations

=== "Adjacency List"

    ```python
    # Most common — efficient for sparse graphs
    # Space: O(V + E)

    from collections import defaultdict

    graph = defaultdict(list)

    # Undirected
    def add_edge_undirected(graph, u, v):
        graph[u].append(v)
        graph[v].append(u)

    # Directed
    def add_edge_directed(graph, u, v):
        graph[u].append(v)

    # Weighted directed
    def add_edge_weighted(graph, u, v, weight):
        graph[u].append((v, weight))

    # Example
    add_edge_undirected(graph, 'A', 'B')
    add_edge_undirected(graph, 'A', 'C')
    add_edge_undirected(graph, 'B', 'D')
    print(dict(graph))
    ```

    **Output:**
    ```
    {'A': ['B', 'C'], 'B': ['A', 'D'], 'C': ['A'], 'D': ['B']}

    Visualised:
    A ── B ── D
    |
    C
    ```

=== "Adjacency Matrix"

    ```python
    # Best for dense graphs or when you need O(1) edge lookup
    # Space: O(V²) — expensive for large sparse graphs

    V = 4  # number of vertices
    matrix = [[0] * V for _ in range(V)]

    def add_edge_matrix(matrix, u, v, weight=1):
        matrix[u][v] = weight
        matrix[v][u] = weight   # remove for directed graph

    add_edge_matrix(matrix, 0, 1)
    add_edge_matrix(matrix, 0, 2)
    add_edge_matrix(matrix, 1, 3)
    ```

    **Output:**
    ```
         0  1  2  3
    0  [ 0, 1, 1, 0 ]
    1  [ 1, 0, 0, 1 ]
    2  [ 1, 0, 0, 0 ]
    3  [ 0, 1, 0, 0 ]

    matrix[0][1] == 1 → edge exists between 0 and 1 ✅
    matrix[0][3] == 0 → no direct edge between 0 and 3
    ```

=== "When to use which"

    | | Adjacency List | Adjacency Matrix |
    |---|---|---|
    | **Space** | O(V + E) | O(V²) |
    | **Check edge (u,v)** | O(degree) | **O(1)** |
    | **Get neighbours** | **O(degree)** | O(V) |
    | **Add edge** | O(1) | O(1) |
    | **Best for** | Sparse graphs, BFS/DFS | Dense graphs, Floyd-Warshall |
    | **Most interviews** | ✅ Default choice | Only when asked |

---

## 2️⃣ BFS — Breadth-First Search

Explores level by level using a **queue**. Finds the **shortest path** (in unweighted graphs) and is ideal for anything involving minimum steps or layers.

```python
from collections import deque

def bfs(graph, start):
    visited = set()
    queue = deque([start])
    visited.add(start)
    order = []

    while queue:
        node = queue.popleft()
        order.append(node)

        for neighbour in graph[node]:
            if neighbour not in visited:
                visited.add(neighbour)
                queue.append(neighbour)

    return order
```

**Step-by-step visualization:**
```
Graph:    A ── B ── D
          |         |
          C ────────┘

BFS from A:
Queue: [A]         visited: {A}
Pop A  → add B, C  Queue: [B, C]      order: [A]
Pop B  → add D     Queue: [C, D]      order: [A, B]
Pop C  → D already Queue: [D]         order: [A, B, C]
Pop D  →           Queue: []          order: [A, B, C, D]
```

**Output:**
```python
bfs(graph, 'A')   # → ['A', 'B', 'C', 'D']
```

### BFS Shortest Path

```python
def bfs_shortest_path(graph, start, end):
    if start == end:
        return [start]
    visited = {start}
    queue = deque([[start]])        # queue of paths, not nodes

    while queue:
        path = queue.popleft()
        node = path[-1]
        for neighbour in graph[node]:
            if neighbour == end:
                return path + [neighbour]
            if neighbour not in visited:
                visited.add(neighbour)
                queue.append(path + [neighbour])
    return []                       # no path found
```

**Output:**
```python
bfs_shortest_path(graph, 'A', 'D')   # → ['A', 'B', 'D']
```

!!! info "BFS = shortest path in unweighted graphs"
    Because BFS explores all nodes at distance 1 before distance 2, the first time it reaches a node is guaranteed to be via the shortest path.

---

## 3️⃣ DFS — Depth-First Search

Explores as deep as possible before backtracking. Uses a **stack** (or recursion). Ideal for cycle detection, topological sort, and connected components.

=== "Recursive"

    ```python
    def dfs_recursive(graph, node, visited=None):
        if visited is None:
            visited = set()
        visited.add(node)
        result = [node]
        for neighbour in graph[node]:
            if neighbour not in visited:
                result += dfs_recursive(graph, neighbour, visited)
        return result
    ```

=== "Iterative"

    ```python
    def dfs_iterative(graph, start):
        visited = set()
        stack = [start]
        order = []

        while stack:
            node = stack.pop()
            if node not in visited:
                visited.add(node)
                order.append(node)
                for neighbour in graph[node]:
                    if neighbour not in visited:
                        stack.append(neighbour)

        return order
    ```

=== "Step-by-step"

    ```
    Graph:    A ── B ── D
              |
              C

    DFS from A (iterative):
    Stack: [A]
    Pop A  → push B, C   Stack: [B, C]    order: [A]
    Pop C  →             Stack: [B]       order: [A, C]
    Pop B  → push D      Stack: [D]       order: [A, C, B]
    Pop D  →             Stack: []        order: [A, C, B, D]

    Note: DFS order depends on neighbour order + stack LIFO
    Recursive DFS gives: [A, B, D, C]
    ```

**Output:**
```python
dfs_recursive(graph, 'A')    # → ['A', 'B', 'D', 'C']
dfs_iterative(graph, 'A')    # → ['A', 'C', 'B', 'D']
```

---

### BFS vs DFS — When to Use Each

=== "Use BFS when"

    ```
    ✅ Shortest path in unweighted graph
    ✅ Minimum number of steps/moves
    ✅ Level-by-level processing
    ✅ Finding nearest neighbour
    ✅ Web crawlers (explore by proximity)

    Examples:
    - Word ladder (minimum transformations)
    - 0/1 matrix (distance to nearest 0)
    - Rotting oranges (minutes to spread)
    - Binary tree level-order traversal
    ```

=== "Use DFS when"

    ```
    ✅ Detect cycles
    ✅ Topological sort
    ✅ Connected components
    ✅ Path existence (not shortest)
    ✅ Backtracking / exhaustive search
    ✅ Maze solving

    Examples:
    - Course schedule (cycle in DAG?)
    - Number of islands
    - Clone graph
    - All paths from source to target
    ```

=== "Complexity"

    | | BFS | DFS |
    |---|---|---|
    | **Time** | O(V + E) | O(V + E) |
    | **Space** | O(V) — queue | O(V) — stack/recursion |
    | **Shortest path** | ✅ Unweighted | ❌ |
    | **Memory (wide graph)** | Bad — big queue | Better |
    | **Memory (deep graph)** | Better | Bad — deep stack |

---

## 4️⃣ Cycle Detection

=== "Undirected Graph (DFS)"

    ```python
    def has_cycle_undirected(graph):
        visited = set()

        def dfs(node, parent):
            visited.add(node)
            for neighbour in graph[node]:
                if neighbour not in visited:
                    if dfs(neighbour, node):
                        return True
                elif neighbour != parent:   # visited AND not parent → cycle
                    return True
            return False

        for node in graph:
            if node not in visited:
                if dfs(node, None):
                    return True
        return False
    ```

    !!! tip "Why check `neighbour != parent`?"
        In an undirected graph, every edge goes both ways. When at node B coming from A, A will appear as a neighbour — but that's not a cycle, just the edge we came from. Only flag a cycle if we see a *different* already-visited node.

=== "Directed Graph (DFS + rec_stack)"

    ```python
    def has_cycle_directed(graph):
        visited = set()
        rec_stack = set()   # nodes in current DFS path

        def dfs(node):
            visited.add(node)
            rec_stack.add(node)
            for neighbour in graph[node]:
                if neighbour not in visited:
                    if dfs(neighbour):
                        return True
                elif neighbour in rec_stack:  # back edge → cycle
                    return True
            rec_stack.remove(node)
            return False

        for node in graph:
            if node not in visited:
                if dfs(node):
                    return True
        return False
    ```

    ```
    Directed cycle example:
    A ──► B ──► C
          ▲     |
          └─────┘    C → B creates a back edge → cycle detected ✅
    ```

---

## 5️⃣ Topological Sort

Only valid on **DAGs** (Directed Acyclic Graphs). Produces an ordering of vertices such that for every edge u → v, u comes before v. Used for task scheduling, build systems, course prerequisites.

=== "Kahn's Algorithm (BFS)"

    ```python
    from collections import deque, defaultdict

    def topological_sort_kahn(vertices, edges):
        graph = defaultdict(list)
        in_degree = {v: 0 for v in vertices}

        for u, v in edges:
            graph[u].append(v)
            in_degree[v] += 1

        # Start with nodes that have no prerequisites
        queue = deque([v for v in vertices if in_degree[v] == 0])
        order = []

        while queue:
            node = queue.popleft()
            order.append(node)
            for neighbour in graph[node]:
                in_degree[neighbour] -= 1
                if in_degree[neighbour] == 0:
                    queue.append(neighbour)

        if len(order) != len(vertices):
            return []       # cycle detected — topological sort impossible
        return order
    ```

    **Output:**
    ```python
    vertices = ['A', 'B', 'C', 'D']
    edges = [('A', 'B'), ('A', 'C'), ('B', 'D'), ('C', 'D')]
    topological_sort_kahn(vertices, edges)
    # → ['A', 'B', 'C', 'D']  or  ['A', 'C', 'B', 'D']
    ```

=== "DFS-based"

    ```python
    def topological_sort_dfs(graph, vertices):
        visited = set()
        stack = []

        def dfs(node):
            visited.add(node)
            for neighbour in graph[node]:
                if neighbour not in visited:
                    dfs(neighbour)
            stack.append(node)      # add AFTER visiting all descendants

        for v in vertices:
            if v not in visited:
                dfs(v)

        return stack[::-1]          # reverse gives topological order
    ```

=== "Visualization"

    ```
    Course prerequisites (edges = must take first):
    Math ──► Algorithms ──► ML
    Math ──► Stats       ──► ML

    in_degree:  Math=0, Algorithms=1, Stats=1, ML=2

    Step 1: queue=[Math]  (in_degree 0)
    Step 2: process Math  → Algorithms(0), Stats(0)  queue=[Alg, Stats]
    Step 3: process Alg   → ML(1)                    queue=[Stats]
    Step 4: process Stats → ML(0)                    queue=[ML]
    Step 5: process ML    →                          queue=[]

    Order: [Math, Algorithms, Stats, ML] ✅
    ```

---

## 6️⃣ Connected Components

```python
def count_components(n, edges):
    graph = defaultdict(list)
    for u, v in edges:
        graph[u].append(v)
        graph[v].append(u)

    visited = set()
    count = 0

    def dfs(node):
        visited.add(node)
        for neighbour in graph[node]:
            if neighbour not in visited:
                dfs(neighbour)

    for node in range(n):
        if node not in visited:
            dfs(node)
            count += 1          # each new DFS = new component

    return count
```

**Output:**
```
Nodes: 0,1,2,3,4
Edges: (0,1),(1,2),(3,4)

Component 1: {0, 1, 2}
Component 2: {3, 4}

count_components(5, [(0,1),(1,2),(3,4)]) → 2
```

---

## 7️⃣ Shortest Path Algorithms

### Dijkstra's Algorithm — O((V + E) log V)

Finds shortest paths from a source to all vertices in a **weighted graph with non-negative weights**. Uses a min-heap as a priority queue.

```python
import heapq
from collections import defaultdict

def dijkstra(graph, start):
    # graph: {node: [(neighbour, weight), ...]}
    dist = defaultdict(lambda: float('inf'))
    dist[start] = 0
    heap = [(0, start)]    # (distance, node)

    while heap:
        d, node = heapq.heappop(heap)

        if d > dist[node]:
            continue        # stale entry — already found shorter path

        for neighbour, weight in graph[node]:
            new_dist = dist[node] + weight
            if new_dist < dist[neighbour]:
                dist[neighbour] = new_dist
                heapq.heappush(heap, (new_dist, neighbour))

    return dict(dist)
```

**Step-by-step:**
```
Graph:   A ──2── B ──1── D
         |               |
         └──4── C ──1────┘

dijkstra(graph, 'A'):
heap: [(0,A)]   dist: {A:0}

Pop (0,A) → relax B(2), C(4)    heap: [(2,B),(4,C)]  dist: {A:0,B:2,C:4}
Pop (2,B) → relax D(3)          heap: [(3,D),(4,C)]  dist: {A:0,B:2,C:4,D:3}
Pop (3,D) → relax C(4) no better heap: [(4,C)]
Pop (4,C) → nothing cheaper

Result: {A:0, B:2, C:4, D:3}
Shortest A→D = 3  (A→B→D) ✅
```

**Output:**
```python
graph = defaultdict(list)
graph['A'] += [('B', 2), ('C', 4)]
graph['B'] += [('D', 1)]
graph['C'] += [('D', 1)]
graph['D'] += []

dijkstra(graph, 'A')
# → {'A': 0, 'B': 2, 'C': 4, 'D': 3}
```

!!! warning "Dijkstra fails with negative weights"
    If any edge has a negative weight, use **Bellman-Ford** instead. Dijkstra's greedy assumption (the smallest known distance is final) breaks with negative edges.

---

### Bellman-Ford — O(V × E)

Handles **negative weights** and detects **negative cycles**. Slower than Dijkstra but more general.

```python
def bellman_ford(vertices, edges, start):
    # edges: list of (u, v, weight)
    dist = {v: float('inf') for v in vertices}
    dist[start] = 0

    # Relax all edges V-1 times
    for _ in range(len(vertices) - 1):
        for u, v, w in edges:
            if dist[u] + w < dist[v]:
                dist[v] = dist[u] + w

    # Check for negative cycles
    for u, v, w in edges:
        if dist[u] + w < dist[v]:
            return None     # negative cycle detected

    return dist
```

**Output:**
```python
vertices = ['A', 'B', 'C', 'D']
edges = [('A','B',2),('A','C',4),('B','D',1),('C','D',1)]
bellman_ford(vertices, edges, 'A')
# → {'A': 0, 'B': 2, 'C': 4, 'D': 3}
```

---

## 8️⃣ Union-Find (Disjoint Set Union)

A data structure that efficiently answers: **are two nodes in the same connected component?** Used for Kruskal's MST, detecting cycles, and network connectivity.

```python
class UnionFind:
    def __init__(self, n):
        self.parent = list(range(n))   # each node is its own parent
        self.rank = [0] * n            # tree height hint

    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])  # path compression
        return self.parent[x]

    def union(self, x, y):
        px, py = self.find(x), self.find(y)
        if px == py:
            return False    # already connected — adding edge creates cycle
        # Union by rank: attach smaller tree under larger
        if self.rank[px] < self.rank[py]:
            px, py = py, px
        self.parent[py] = px
        if self.rank[px] == self.rank[py]:
            self.rank[px] += 1
        return True

    def connected(self, x, y):
        return self.find(x) == self.find(y)
```

**Visualization:**
```
Initial:  parent = [0, 1, 2, 3, 4]   (5 separate components)

union(0,1):   0──1    parent = [0, 0, 2, 3, 4]
union(2,3):   0──1  2──3    parent = [0, 0, 2, 2, 4]
union(0,2):   0──1──2──3    parent = [0, 0, 0, 2, 4]
               ↑ all point toward root 0

connected(1,3) → find(1)=0, find(3)=0 → True ✅
connected(1,4) → find(1)=0, find(4)=4 → False ✅
```

**Output:**
```python
uf = UnionFind(5)
uf.union(0, 1)
uf.union(2, 3)
uf.union(0, 2)
print(uf.connected(1, 3))   # → True
print(uf.connected(1, 4))   # → False
```

!!! info "Path compression + union by rank = nearly O(1)"
    With both optimisations, each `find` and `union` runs in O(α(n)) — the inverse Ackermann function, effectively constant for all practical inputs.

---

## ✅ Quick Reference Summary

| Algorithm | Time | Space | Use case |
|---|---|---|---|
| BFS | O(V + E) | O(V) | Shortest path (unweighted), level traversal |
| DFS | O(V + E) | O(V) | Cycle detection, topo sort, components |
| Dijkstra | O((V+E) log V) | O(V) | Shortest path, non-negative weights |
| Bellman-Ford | O(V × E) | O(V) | Shortest path, negative weights |
| Topological Sort | O(V + E) | O(V) | DAG ordering, scheduling |
| Union-Find | O(α(n)) per op | O(V) | Connectivity, cycle detection |

| Problem pattern | Algorithm |
|---|---|
| Shortest path, unweighted | BFS |
| Shortest path, weighted ≥ 0 | Dijkstra |
| Shortest path, negative weights | Bellman-Ford |
| Detect cycle, undirected | DFS (check parent) |
| Detect cycle, directed | DFS (recursion stack) |
| Task ordering / prerequisites | Topological sort (Kahn's) |
| Number of islands / components | DFS / BFS flood fill |
| Dynamic connectivity | Union-Find |
| Minimum spanning tree | Kruskal (Union-Find) / Prim |

!!! warning "Key interview gotchas"
    - Always use a `visited` set to avoid infinite loops in cyclic graphs
    - For disconnected graphs, loop over **all** vertices to start BFS/DFS — not just one
    - Dijkstra needs a **min-heap** — don't use a plain queue or it becomes O(V²)
    - Topological sort is only valid on **DAGs** — if Kahn's returns fewer nodes than V, there's a cycle
    - Union-Find detects cycles in **undirected** graphs — for directed use DFS + recursion stack
    - BFS shortest path counts **edges**, not weights — for weighted use Dijkstra
    - In grid problems (islands, oranges), the graph is implicit — neighbours are up/down/left/right cells