# 11 — Greedy Algorithms

> **Files in project:** `spanning tree/kruskal.py` · `spanning tree/prims.py` · `spanning tree/dijkstra.py`  
> **Status:** Three greedy algorithms implemented

---

## 🧠 Concept Overview

A **greedy algorithm** builds a solution step by step, always making the **locally optimal choice** at each step — the choice that looks best right now — without considering future consequences.

Greedy algorithms are fast and simple, but they don't always produce the globally optimal solution. The art is knowing **when greedy is correct** — which requires a proof (exchange argument or greedy stays ahead).

**Greedy vs DP:** If a greedy choice leads to an optimal solution provably, use greedy. If you need to compare all options, use DP.

---

## 📖 Key Terminology

| Term | Meaning |
|------|---------|
| Greedy Choice | The locally optimal selection at each step |
| Greedy Choice Property | A globally optimal solution can be reached by locally optimal choices |
| Optimal Substructure | Optimal solution includes optimal sub-solutions |
| Exchange Argument | Proof technique: show swapping greedy choice for any other makes things worse |
| Matroid | Mathematical structure where greedy gives optimal solution |

---

## 🔧 Algorithms Implemented

---

### 1. Dijkstra's Algorithm (`dijkstra.py`)

Dijkstra is a **greedy** shortest-path algorithm: at each step, it greedily picks the unvisited vertex with the smallest known distance.

**Greedy choice:** The vertex with minimum `cost` that hasn't been finalized.

**Why greedy works here:** Once a vertex is finalized (all edge weights non-negative), no future path can improve its cost. The greedy "minimum cost" pick is provably safe.

See Topic 06 for full implementation details.

---

### 2. Kruskal's Algorithm (`kruskal.py`)

**Problem:** Build a Minimum Spanning Tree (MST).

**Greedy strategy:** Always add the cheapest edge that doesn't create a cycle.

```python
def KRUSKAL(G):
    MAKE_SET(G)
    PQ = BUILD_PRIORITY_QUEUE(G)   # edges sorted cheapest-first
    MST = []
    while not PQ.empty() and len(MST) < len(G) - 1:
        w, u, v = PQ.get()          # cheapest remaining edge
        if FIND(u) != FIND(v):      # greedy check: no cycle?
            UNION(u, v)
            MST.append((u, v, w))
    return MST
```

**Why greedy works:** Cut property — for any cut of the graph, the minimum-weight crossing edge must be in some MST. Kruskal always picks such an edge.

**Union-Find (Disjoint Set Union)** makes cycle detection efficient:
- `MAKE_SET`: Each vertex is its own component.
- `FIND(v)`: Returns the root of v's component (with path compression).
- `UNION(u, v)`: Merges two components (by rank for efficiency).

| Property | Value |
|----------|-------|
| Time | O(E log E) — sorting dominates |
| Space | O(V) for Union-Find |

---

### 3. Prim's Algorithm (`prims.py`)

**Problem:** Build a Minimum Spanning Tree (MST).

**Greedy strategy:** Grow the MST one vertex at a time. Always add the cheapest edge that connects a new vertex to the existing MST.

```python
def prim_algorithm(G, start):
    cost = {v: float('inf') for v in G}
    parent = {v: None for v in G}
    cost[start] = 0
    in_MST = set()
    MST = []
    while len(in_MST) < len(G):
        # Greedy choice: min-cost vertex not yet in MST
        u = min((v for v in G if v not in in_MST), key=lambda v: cost[v])
        in_MST.add(u)
        if parent[u]:
            MST.append((parent[u], u, cost[u]))
        # Update costs for u's neighbors
        for v, w in G[u].items():
            if v not in in_MST and w < cost[v]:
                cost[v] = w
                parent[v] = u
    return MST
```

**Key difference from Kruskal:**
- **Kruskal** works on edges globally — good for sparse graphs.
- **Prim** grows a connected tree from a seed — good for dense graphs.

Both produce a valid MST with the same total weight.

| Prim Property | Value |
|---------------|-------|
| Time (array-based, this implementation) | O(V²) |
| Time (binary heap PQ) | O(E log V) |
| Time (Fibonacci heap) | O(E + V log V) |

---

## 🔧 Other Classic Greedy Problems (Study Topics)

---

### Activity Selection / Interval Scheduling

**Problem:** Given n activities with start/end times, select the maximum number of non-overlapping activities.

**Greedy:** Always pick the activity with the earliest finish time.

```python
def activity_selection(activities):
    activities.sort(key=lambda x: x[1])   # sort by finish time
    selected = [activities[0]]
    for start, finish in activities[1:]:
        if start >= selected[-1][1]:       # doesn't overlap last selected
            selected.append((start, finish))
    return selected
```

**Proof:** Earliest-finish-time greedy leaves maximum room for future activities.

---

### Huffman Coding (Data Compression)

**Problem:** Assign variable-length binary codes to characters; more frequent characters get shorter codes.

**Greedy:** Always merge the two least-frequent nodes in a priority queue.

```
Characters: A(5) B(3) C(1) D(1) E(2)
Build tree bottom-up: always combine two smallest frequencies
```

Result: prefix-free codes. Used in ZIP, PNG, MP3.

---

### Fractional Knapsack

**Problem:** Items with weight/value; can take fractions. Maximize value.

**Greedy:** Take items in decreasing order of value/weight ratio. Take as much of each as fits.

**Note:** This greedy works for fractional knapsack. The 0/1 knapsack (no fractions) requires DP — greedy fails.

---

### Job Scheduling with Deadlines

**Problem:** n jobs each take 1 unit of time, have deadlines and profits. Maximize profit.

**Greedy:** Sort by decreasing profit; schedule each job in the latest available slot before its deadline.

---

## 📐 When Does Greedy Work?

| Problem | Greedy Works? | Why |
|---------|--------------|-----|
| Activity Selection | ✅ | Exchange argument |
| Fractional Knapsack | ✅ | Greedy ratio optimal |
| 0/1 Knapsack | ❌ | Must compare all options (use DP) |
| Huffman Coding | ✅ | Proved by contradiction |
| Dijkstra | ✅ | Non-negative weights only |
| Bellman-Ford | N/A (not greedy) | Uses DP/relaxation |
| Coin change (canonical coins) | ✅ (USD) | Specific to coin system |
| Coin change (arbitrary) | ❌ | Need DP |
| MST (Kruskal/Prim) | ✅ | Cut property proof |

---

## ⚠️ Common Pitfalls

1. **Assuming greedy works without proof** — always verify with exchange argument or counterexample.
2. **Greedy for 0/1 knapsack** — a classic mistake; high-ratio items might not fit, leaving capacity wasted.
3. **Prim on disconnected graph** — algorithm may not visit all vertices; check for disconnectedness first.
4. **Kruskal directed graph** — standard Kruskal is for undirected graphs; directed MST needs Edmonds' algorithm.

---

## 💡 Practice Problems

1. Minimum number of platforms at a train station.
2. Gas station — can you complete the circuit?
3. Jump game — can you reach the last index?
4. Assign cookies to children maximizing satisfaction.
5. Meeting rooms II — minimum number of rooms needed.
6. Reorganize string — no two adjacent characters same.

---

## 📚 Further Reading

- CLRS Chapter 16 — Greedy Algorithms
- *Algorithm Design* — Kleinberg & Tardos, Chapter 4
- Proof techniques: exchange argument, greedy stays ahead
