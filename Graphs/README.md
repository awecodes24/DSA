# 06 — Graphs: BFS, DFS, Shortest Path & MST

> **Files in project:** `spanning tree/bfs.py` · `spanning tree/dfs.py` · `spanning tree/dijkstra.py` · `spanning tree/kruskal.py` · `spanning tree/prims.py` · `spanning tree/FloydWarshall.py` · `dijkstra.ipynb`

---

## 🧠 Concept Overview

A **graph** G = (V, E) is a set of **vertices** (nodes) V and **edges** E connecting pairs of vertices. Graphs model relationships: road networks, social connections, web links, dependency trees, and electrical circuits are all graphs.

Unlike trees, graphs can have **cycles**, **multiple paths** between nodes, and **disconnected components**.

---

## 📖 Key Terminology

| Term | Meaning |
|------|---------|
| Vertex / Node | A point in the graph |
| Edge | A connection between two vertices |
| Directed (Digraph) | Edges have direction: u → v ≠ v → u |
| Undirected | Edges have no direction: u — v |
| Weighted | Each edge has a numeric weight/cost |
| Adjacent | Two vertices connected by an edge |
| Degree | Number of edges at a vertex |
| Path | Sequence of vertices connected by edges |
| Cycle | A path that starts and ends at the same vertex |
| Connected | Every vertex reachable from every other |
| Spanning Tree | A tree that includes all vertices of a connected graph |
| MST | Minimum Spanning Tree — spanning tree with minimum total weight |
| DAG | Directed Acyclic Graph — no cycles, used for scheduling |

---

## 🗂️ Graph Representation

The project uses **adjacency lists** as Python dicts:

```python
G = {
    's': {'t': 10, 'y': 5},
    't': {'y': 2,  'x': 1},
    'x': {'z': 6},
    'y': {'x': 9,  't': 3, 'z': 2},
    'z': {'x': 6,  's': 7}
}
# G[u][v] = weight of edge u→v
```

**Adjacency Matrix** (alternative):
```
     s    t    y    x    z
s  [ 0   10    5    ∞    ∞ ]
t  [ ∞    0    2    1    ∞ ]
y  [ ∞    3    0    9    2 ]
x  [ ∞    ∞    ∞    0    6 ]
z  [ 7    ∞    ∞    6    0 ]
```

| Representation | Space | Edge check | Neighbor iteration |
|----------------|-------|-----------|-------------------|
| Adjacency List | O(V+E) | O(degree) | O(degree) |
| Adjacency Matrix | O(V²) | O(1) | O(V) |

Use lists for sparse graphs; matrices for dense graphs.

---

## 🔧 Graph Traversals

### BFS — Breadth-First Search (`bfs.py`)

**Idea:** Explore all neighbors at the current depth before going deeper. Uses a **queue**.

```python
def BFS(G, start):
    visited = set()
    q = Queue()
    order = []
    q.put(start)
    visited.add(start)
    while not q.empty():
        u = q.get()
        order.append(u)
        for v in G[u]:
            if v not in visited:
                q.put(v)
                visited.add(v)
    return order
```

**BFS trace from 's':** `s → t → y → x → z`

**Properties:**
- Explores in waves of increasing distance from source.
- **Finds shortest path** (fewest edges) from source to all reachable vertices.
- Time: O(V + E), Space: O(V)

**Applications:** Shortest path (unweighted), social network distance, web crawlers, level-order tree traversal.

---

### DFS — Depth-First Search (`dfs.py`)

**Idea:** Explore as deep as possible along each branch before backtracking. Uses a **stack** (or recursion).

```python
def DFS_iterative(G, start):
    visited = set()
    stack = [start]
    order = []
    while stack:
        u = stack.pop()
        if u not in visited:
            visited.add(u)
            order.append(u)
            for v in reversed(G[u]):   # reversed for consistent ordering
                if v not in visited:
                    stack.append(v)
    return order
```

**Properties:**
- Time: O(V + E), Space: O(V)
- Naturally discovers back edges (cycles) and finish times (for topological sort).

**Applications:** Topological sort, cycle detection, strongly connected components (Kosaraju/Tarjan), maze solving, puzzle solving.

---

## 🔧 Shortest Path Algorithms

### Dijkstra's Algorithm (`dijkstra.py`)

**Problem:** Find the shortest path from a single source to all other vertices in a **weighted, non-negative** graph.

**Key idea:** Greedy. Always process the vertex with the smallest tentative distance next.

```python
def DJ(G, s):
    cost, prev = INITIALIZE_SINGLE_SOURCE(G, s)
    # cost[s] = 0, cost[others] = ∞
    PQ = PriorityQueue()
    for vertex in G:
        PQ.put((cost[vertex], vertex))
    visited = []
    while len(visited) != len(G):
        _, u = PQ.get()          # extract minimum cost vertex
        visited.append(u)
        for v in G[u]:
            if v not in visited:
                # RELAX edge (u, v)
                if cost[v] > cost[u] + G[u][v]:
                    cost[v] = cost[u] + G[u][v]
                    prev[v] = u
                    PQ.put((cost[v], v))
    return cost, prev
```

**Trace on the project graph from 's':**

| Step | Processed | cost[s] | cost[t] | cost[y] | cost[x] | cost[z] |
|------|-----------|---------|---------|---------|---------|---------|
| Init | — | 0 | ∞ | ∞ | ∞ | ∞ |
| 1 | s | 0 | 10 | 5 | ∞ | ∞ |
| 2 | y | 0 | 8 | 5 | 14 | 7 |
| 3 | z | 0 | 8 | 5 | 13 | 7 |
| 4 | t | 0 | 8 | 5 | 9 | 7 |
| 5 | x | 0 | 8 | 5 | 9 | 7 |

**Path reconstruction:**
```python
def reconstruct_path(vertex, prev):
    path = vertex
    while prev[vertex] != ' ':
        path = prev[vertex] + '->' + path
        vertex = prev[vertex]
    return path
```

| Property | Value |
|----------|-------|
| Time (binary heap PQ) | O((V + E) log V) |
| Time (Fibonacci heap) | O(E + V log V) |
| Space | O(V) |
| Handles negative weights | ❌ No — use Bellman-Ford |

---

### Floyd-Warshall Algorithm (`FloydWarshall.py`)

**Problem:** Find shortest paths between **all pairs** of vertices.

**Idea:** Dynamic programming. For each intermediate vertex k, check if routing through k improves the current best path between every pair (i, j).

```python
def FW(W):
    n = len(W)
    D = {0: W}
    for k in range(n):
        D[k+1] = D[k]  # build on previous iteration
        for i in range(n):
            for j in range(n):
                D[k+1][i][j] = min(
                    D[k][i][j],           # don't use k
                    D[k][i][k] + D[k][k][j]  # route through k
                )
```

**Recurrence:** `D^(k)[i][j] = min(D^(k-1)[i][j], D^(k-1)[i][k] + D^(k-1)[k][j])`

| Property | Value |
|----------|-------|
| Time | **O(V³)** |
| Space | O(V²) |
| Handles negative weights | ✅ Yes (not negative cycles) |
| Output | All-pairs shortest path matrix |

---

## 🔧 Minimum Spanning Tree (MST)

A **Minimum Spanning Tree** of a connected weighted undirected graph connects all vertices with the minimum total edge weight, using exactly V−1 edges and no cycles.

---

### Kruskal's Algorithm (`kruskal.py`)

**Greedy approach:** Sort all edges by weight, greedily add edges that don't create a cycle. Uses **Union-Find (Disjoint Set Union)** to detect cycles efficiently.

```python
def KRUSKAL(G):
    MAKE_SET(G)          # each vertex is its own component
    PQ = BUILD_PRIORITY_QUEUE(G)   # edges sorted by weight
    MST = []
    while not PQ.empty() and len(MST) < len(G) - 1:
        w, u, v = PQ.get()
        if FIND(u) != FIND(v):   # no cycle
            UNION(u, v)
            MST.append((u, v, w))
    return MST
```

**Union-Find with path compression and union by rank:**
```python
def FIND(v):
    if parent[v] != v:
        parent[v] = FIND(parent[v])  # path compression
    return parent[v]

def UNION(u, v):
    root_u, root_v = FIND(u), FIND(v)
    if rank[root_u] < rank[root_v]:
        parent[root_u] = root_v
    elif rank[root_u] > rank[root_v]:
        parent[root_v] = root_u
    else:
        parent[root_v] = root_u
        rank[root_u] += 1
```

Time: O(E log E) — dominated by sorting edges.

---

### Prim's Algorithm (`prims.py`)

**Greedy approach:** Grow the MST one vertex at a time. Always add the minimum-weight edge that connects the MST to a vertex not yet in the MST.

```python
def prim_algorithm(G, start):
    cost = {v: float('inf') for v in G}
    parent = {v: None for v in G}
    cost[start] = 0
    in_MST = set()
    MST = []
    while len(in_MST) < len(G):
        # Pick min-cost vertex not in MST
        u = min((v for v in G if v not in in_MST), key=lambda v: cost[v])
        in_MST.add(u)
        if parent[u]:
            MST.append((parent[u], u, cost[u]))
        for v, w in G[u].items():
            if v not in in_MST and w < cost[v]:
                cost[v] = w
                parent[v] = u
    return MST
```

Time: O(V²) with array; O(E log V) with binary heap.

---

## 📐 Algorithm Selection Guide

| Problem | Algorithm | Complexity |
|---------|-----------|-----------|
| Unweighted shortest path | BFS | O(V+E) |
| Single-source, non-neg weights | Dijkstra | O((V+E)log V) |
| Single-source, any weights | Bellman-Ford | O(VE) |
| All-pairs shortest paths | Floyd-Warshall | O(V³) |
| MST — sparse graph | Kruskal | O(E log E) |
| MST — dense graph | Prim | O(E log V) |
| Cycle detection | DFS | O(V+E) |
| Topological sort | DFS / Kahn's | O(V+E) |

---

## ⚠️ Common Pitfalls

1. **Dijkstra with negative edges** — gives wrong answers; use Bellman-Ford.
2. **Unvisited check in Dijkstra** — the project's implementation re-inserts into PQ; must check if vertex already processed.
3. **Directed vs undirected** — Kruskal as written works on directed; MST is for undirected. Treat directed graph MST as "minimum spanning arborescence" (Edmonds' algorithm).
4. **Disconnected graphs** — BFS/DFS from one source won't visit disconnected components; iterate over all vertices.

---

## 💡 Practice Problems

1. Find all connected components of a graph.
2. Detect a cycle in a directed graph using DFS.
3. Topological sort a DAG (course prerequisites).
4. Find shortest path in a maze (BFS).
5. Find all bridges and articulation points in a graph.
6. Implement Bellman-Ford for graphs with negative weights.

---

## 📚 Further Reading

- CLRS Chapters 22–25 (all graph algorithms)
- [Graph algorithm visualizations](https://visualgo.net/en/graphds)
- Skiena, *The Algorithm Design Manual* — Chapter 6–8
