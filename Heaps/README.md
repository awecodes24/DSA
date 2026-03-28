# 09 — Heaps & Priority Queues

> **Files in project:** `Sorting/heap.c` (Heap Sort implementation)  
> **Status:** Heap Sort implemented; full heap ADT is a study guide extension

---

## 🧠 Concept Overview

A **heap** is a complete binary tree stored in an array where every parent node satisfies the **heap property**:
- **Max-Heap:** Parent ≥ Children — root is the maximum.
- **Min-Heap:** Parent ≤ Children — root is the minimum.

Heaps power **priority queues** (always access the highest-priority element) and **heap sort** (guaranteed O(n log n), O(1) space).

---

## 📖 Key Terminology

| Term | Meaning |
|------|---------|
| Complete Binary Tree | Every level full except possibly the last, filled left to right |
| Heap Property | Parent ≥ (max-heap) or ≤ (min-heap) all children |
| Heapify | Restore heap property after a change |
| Build-Heap | Convert an arbitrary array into a valid heap |
| Extract-Max/Min | Remove the root and restore heap property |
| Insert | Add an element and bubble it up |
| Sift-Down / Heapify-Down | Push a node down to its correct level |
| Sift-Up / Heapify-Up | Bubble a node up to its correct level |
| Priority Queue | ADT providing Insert and Extract-Max/Min in O(log n) |

---

## 🔧 Array Representation

A complete binary tree maps perfectly to an array — no pointers needed:

```
       16
      /   \
    14     10
   / \    / \
  8   7  9   3
 / \
2   4

Array: [16, 14, 10, 8, 7, 9, 3, 2, 4]
Index:   0   1   2  3  4  5  6  7  8
```

**Index relationships (1-indexed):**
- Parent of node i: `i / 2`
- Left child of i: `2i`
- Right child of i: `2i + 1`

**Index relationships (0-indexed, as in C arrays):**
- Parent of i: `(i - 1) / 2`
- Left child: `2i + 1`
- Right child: `2i + 2`

---

## 🔧 Core Operations

### Heapify (Sift-Down)

Assumes both subtrees are valid heaps but root may violate. Fix by swapping down:

```c
void heapify(int A[], int n, int i) {
    int largest = i;
    int left  = 2 * i + 1;
    int right = 2 * i + 2;

    if (left < n && A[left] > A[largest])
        largest = left;
    if (right < n && A[right] > A[largest])
        largest = right;

    if (largest != i) {
        swap(A[i], A[largest]);
        heapify(A, n, largest);   // recursively fix the subtree
    }
}
```

Time: O(log n) — tree height.

### Build-Heap

Convert an arbitrary array into a max-heap by running `heapify` on all non-leaf nodes, bottom-up:

```c
void build_heap(int A[], int n) {
    for (int i = n/2 - 1; i >= 0; i--)  // start from last non-leaf
        heapify(A, n, i);
}
```

**Time: O(n)** — surprisingly, not O(n log n). Most nodes at lower levels need very few swaps.

### Insert

```python
def insert(heap, value):
    heap.append(value)       # add at end
    i = len(heap) - 1
    while i > 0:             # sift up
        parent = (i - 1) // 2
        if heap[i] > heap[parent]:
            heap[i], heap[parent] = heap[parent], heap[i]
            i = parent
        else:
            break
```

Time: O(log n)

### Extract-Max

```python
def extract_max(heap):
    if not heap: return None
    max_val = heap[0]
    heap[0] = heap.pop()     # move last element to root
    heapify_down(heap, 0)    # restore heap property
    return max_val
```

Time: O(log n)

---

## 🔧 Heap Sort (`heap.c`)

**Two phases:**
1. **Build max-heap** from the array — O(n).
2. **Sort:** Repeatedly extract the maximum (swap root with last element, reduce heap size, heapify) — O(n log n).

```c
// Phase 1
build_heap(A, n);

// Phase 2
for (int i = n - 1; i > 0; i--) {
    swap(A[0], A[i]);     // root (max) goes to final position
    heapify(A, i, 0);     // restore heap on reduced array [0..i-1]
}
```

**Why it's in-place:** The "sorted" part grows from the right; the "heap" part shrinks from the right. No extra array needed.

| Property | Value |
|----------|-------|
| Time (all cases) | **O(n log n)** |
| Space | **O(1)** |
| Stable | ❌ No |

**Heap Sort vs Quick Sort:** Heap sort has guaranteed O(n log n) but worse cache performance than quicksort's sequential access patterns. In practice, quicksort wins on real hardware.

---

## 🔧 Priority Queue ADT

A **Priority Queue** is an ADT wrapping a heap:

```python
import heapq  # Python's built-in min-heap

pq = []
heapq.heappush(pq, (priority, value))
priority, value = heapq.heappop(pq)   # returns smallest priority

# For max-heap: negate priorities
heapq.heappush(pq, (-priority, value))
```

**Applications of Priority Queues:**
- Dijkstra's shortest path (used in this project's `dijkstra.py`).
- A* pathfinding.
- CPU scheduling (shortest job first).
- Huffman coding (data compression).
- Event-driven simulation.
- Merge k sorted lists.

---

## 🔧 Specialized Heaps (Study Topics)

### Min-Max Heap
Supports both `find-min` and `find-max` in O(1), `delete-min` and `delete-max` in O(log n).

### Binomial Heap
Supports **merge** of two heaps in O(log n). Collection of binomial trees.

### Fibonacci Heap
Supports `decrease-key` in **amortized O(1)**. Used in optimal Dijkstra: O(E + V log V). Complex to implement.

### d-ary Heap
Each node has d children instead of 2. Reduces tree height to log_d(n) — faster extract but slower decrease-key. d=4 is common in practice.

---

## 📐 Complexity Summary

| Operation | Binary Heap | Fibonacci Heap |
|-----------|-------------|---------------|
| Find-Max | O(1) | O(1) |
| Insert | O(log n) | O(1) amortized |
| Extract-Max | O(log n) | O(log n) amortized |
| Decrease-Key | O(log n) | O(1) amortized |
| Delete | O(log n) | O(log n) amortized |
| Merge | O(n) | O(1) |
| Build | **O(n)** | O(n) |

---

## ⚠️ Common Pitfalls

1. **0-indexed vs 1-indexed** — parent/child formulas differ. Choose one and be consistent.
2. **Heapify direction** — sift-down for extract/build; sift-up for insert.
3. **Build-heap starting index** — start from `n/2 - 1`, not `n - 1` (leaves don't need heapifying).
4. **Python's heapq is a min-heap** — negate values for max-heap behavior.

---

## 💡 Practice Problems

1. Implement a min-heap from scratch without using heapq.
2. Find the k largest elements in a stream.
3. Merge k sorted linked lists using a min-heap.
4. Implement a median finder that supports streaming inserts.
5. Rearrange a string so no two adjacent characters are the same.
6. Task scheduler — maximize CPU utilization.

---

## 📚 Further Reading

- CLRS Chapter 6 — Heaps and Priority Queues
- [Heap visualization](https://visualgo.net/en/heap)
- Python `heapq` documentation
