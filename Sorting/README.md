# 04 — Sorting Algorithms

> **Files in project:** `Sorting/bubble.c` · `Sorting/selection.c` · `Sorting/insertion.c` · `Sorting/shell.c` · `Sorting/mergesort.c` · `Sorting/quick.c` · `Sorting/heap.c` · `Sorting/quicksort.py` · `Sorting/selectionsort.py` · `Sorting/radixSort.py`

---

## 🧠 Concept Overview

Sorting is the process of rearranging a sequence of elements into a specified order (typically ascending). It is one of the most studied problems in computer science because:

- Many algorithms depend on sorted input (binary search, merge of sorted lists).
- It provides rich examples of algorithm design paradigms (comparison-based, counting, divide-and-conquer).
- Practical performance differences between algorithms are dramatic.

This project implements **8 sorting algorithms** in C and Python.

---

## 📖 Key Terminology

| Term | Meaning |
|------|---------|
| In-place | Sorts using O(1) extra memory |
| Stable | Preserves relative order of equal elements |
| Comparison sort | Uses `<` / `>` comparisons; lower bound is O(n log n) |
| Non-comparison sort | Uses key properties (digit, range); can beat O(n log n) |
| Adaptive | Faster when input is partially sorted |
| Divide and Conquer | Split, sort each half, combine |
| Partition | Rearrange elements around a pivot |

---

## 🔧 Algorithms Implemented

---

### 1. Bubble Sort (`bubble.c`)

**Idea:** Repeatedly compare adjacent elements; swap if out of order. After each pass, the largest unsorted element "bubbles up" to its correct position.

```c
for (i = 0; i < n-1; i++) {
    for (j = 0; j < n-i-1; j++) {
        if (A[j] > A[j+1]) {
            // swap A[j] and A[j+1]
        }
    }
}
```

**Trace (first pass):** `[5, 3, 8, 1]`
- Compare 5,3 → swap → `[3, 5, 8, 1]`
- Compare 5,8 → no swap → `[3, 5, 8, 1]`
- Compare 8,1 → swap → `[3, 5, 1, 8]` ← 8 is in place

| Property | Value |
|----------|-------|
| Best case | O(n) — with early-exit optimization |
| Average/Worst | O(n²) |
| Space | O(1) |
| Stable | ✅ Yes |
| In-place | ✅ Yes |

**When to use:** Never in production. Its value is pedagogical — it is the simplest possible sort to understand.

---

### 2. Selection Sort (`selection.c`)

**Idea:** Find the minimum in the unsorted portion; swap it to its final position. The sorted portion grows left-to-right.

```c
for (i = 0; i < n-1; i++) {
    min_idx = i;
    for (j = i+1; j < n; j++) {
        if (A[j] < A[min_idx]) min_idx = j;
    }
    swap(A[i], A[min_idx]);
}
```

| Property | Value |
|----------|-------|
| Best/Average/Worst | O(n²) — always exactly n(n-1)/2 comparisons |
| Space | O(1) |
| Stable | ❌ No (can swap equal elements past each other) |
| In-place | ✅ Yes |

**Advantage over bubble:** Exactly n−1 swaps (minimum possible for comparison sort). Good when swaps are expensive.

---

### 3. Insertion Sort (`insertion.c`)

**Idea:** Build the sorted array one element at a time, inserting each new element into its correct position in the already-sorted left portion.

```c
for (i = 1; i < n; i++) {
    key = A[i];
    j = i - 1;
    while (j >= 0 && A[j] > key) {
        A[j+1] = A[j];    // shift right
        j--;
    }
    A[j+1] = key;         // insert
}
```

**Analogy:** How you sort playing cards in your hand.

| Property | Value |
|----------|-------|
| Best case | O(n) — already sorted |
| Average/Worst | O(n²) |
| Space | O(1) |
| Stable | ✅ Yes |
| Adaptive | ✅ Yes |

**When to use:** Excellent for small arrays (n < 20) and nearly-sorted data. Used as the base case in hybrid sorts like Timsort (Python's built-in).

---

### 4. Shell Sort (`shell.c`)

**Idea:** A generalization of insertion sort. Instead of comparing adjacent elements, compare elements that are `gap` positions apart. Start with a large gap, reduce it until gap = 1 (final insertion sort pass on nearly-sorted data).

```c
for (gap = n/2; gap > 0; gap /= 2) {
    for (i = gap; i < n; i++) {
        temp = A[i];
        for (j = i; j >= gap && A[j-gap] > temp; j -= gap)
            A[j] = A[j-gap];
        A[j] = temp;
    }
}
```

| Property | Value |
|----------|-------|
| Best case | O(n log n) |
| Average | O(n^1.25) to O(n^1.5) — gap-sequence dependent |
| Worst | O(n²) — naive gaps |
| Space | O(1) |
| Stable | ❌ No |

**Gap sequences matter:** Hibbard's (1, 3, 7, 15…), Knuth's (1, 4, 13, 40…) give better complexity than n/2.

---

### 5. Merge Sort (`mergesort.c`)

**Paradigm:** Divide and Conquer. Split the array in half, recursively sort each half, merge the two sorted halves.

```c
void Merge_sort(int A[], int l, int r) {
    if (l < r) {
        m = (l + r) / 2;
        Merge_sort(A, l, m);       // sort left half
        Merge_sort(A, m+1, r);     // sort right half
        Merge(A, l, m+1, r);       // merge them
    }
}
```

**Merge step:** Compare front elements of both halves; always take the smaller one into the output array.

```
Left:  [1, 4, 7]
Right: [2, 5, 9]
Merge: [1, 2, 4, 5, 7, 9]  ← always O(n)
```

| Property | Value |
|----------|-------|
| Best/Average/Worst | **O(n log n)** — guaranteed |
| Space | O(n) — needs auxiliary array |
| Stable | ✅ Yes |
| In-place | ❌ No |

**Recurrence:** `T(n) = 2T(n/2) + O(n)` → O(n log n) by Master Theorem.

**When to use:** When guaranteed O(n log n) is required, or when sorting linked lists (no extra space needed for lists).

---

### 6. Quick Sort (`quick.c`)

**Paradigm:** Divide and Conquer. Choose a **pivot**, partition the array so all elements less than pivot are left of it and all greater are right, then recursively sort each side.

```c
int Partition(int A[], int l, int r) {
    int pivot = A[l];   // pivot = leftmost element
    int x = l, y = r;
    while (x < y) {
        while (A[x] <= pivot && x <= r) x++;
        while (A[y] > pivot) y--;
        if (x < y) swap(A[x], A[y]);
    }
    swap(A[l], A[y]);   // place pivot at final position
    return y;
}
```

| Property | Value |
|----------|-------|
| Best/Average | **O(n log n)** |
| Worst | O(n²) — when pivot is always min/max |
| Space | O(log n) stack frames |
| Stable | ❌ No |
| In-place | ✅ Yes |

**Pivot strategies:**
- First element (this project) → worst on already-sorted input.
- Last element → same weakness.
- Random pivot → expected O(n log n), worst still possible but extremely rare.
- Median-of-three → good practical choice.

**Why quicksort is often faster than merge sort in practice:** Better cache locality (in-place), no allocation, smaller constant factors.

---

### 7. Heap Sort (`heap.c`)

**Idea:** Build a max-heap from the array, then repeatedly extract the maximum and place it at the end.

```
Phase 1: Heapify — build max-heap in O(n)
Phase 2: Extract max n times — each extraction O(log n)
Total: O(n log n)
```

| Property | Value |
|----------|-------|
| Best/Average/Worst | **O(n log n)** — guaranteed |
| Space | **O(1)** — in-place |
| Stable | ❌ No |

**Advantage:** In-place with guaranteed O(n log n) — avoids merge sort's O(n) space and quicksort's O(n²) worst case. See Topic 09 (Heaps) for full heap structure details.

---

### 8. Radix Sort (`radixSort.py`)

**Non-comparison sort.** Sorts integers digit by digit, from least significant to most significant (LSD radix sort), using a stable sort (counting sort) at each digit position.

```python
def radix_sort(arr):
    max_val = max(arr)
    exp = 1
    while max_val // exp > 0:
        counting_sort_by_digit(arr, exp)
        exp *= 10
```

| Property | Value |
|----------|-------|
| Time | O(nk) where k = number of digits |
| Space | O(n + k) |
| Stable | ✅ Yes |
| Comparison | ❌ No |

**When to beat O(n log n):** When k is small (e.g., sorting 32-bit integers → k = 10 decimal digits, or k = 4 for base-256).

---

## 📐 Complexity Comparison Table

| Algorithm | Best | Average | Worst | Space | Stable |
|-----------|------|---------|-------|-------|--------|
| Bubble | O(n) | O(n²) | O(n²) | O(1) | ✅ |
| Selection | O(n²) | O(n²) | O(n²) | O(1) | ❌ |
| Insertion | O(n) | O(n²) | O(n²) | O(1) | ✅ |
| Shell | O(n log n) | O(n^1.3) | O(n²) | O(1) | ❌ |
| Merge | O(n log n) | O(n log n) | O(n log n) | O(n) | ✅ |
| Quick | O(n log n) | O(n log n) | O(n²) | O(log n) | ❌ |
| Heap | O(n log n) | O(n log n) | O(n log n) | O(1) | ❌ |
| Radix | O(nk) | O(nk) | O(nk) | O(n+k) | ✅ |

---

## 🏆 Choosing the Right Sort

```
Is n very small (< 20)?            → Insertion Sort
Is data nearly sorted?             → Insertion Sort
Need guaranteed O(n log n)?        → Merge Sort or Heap Sort
Need in-place + fast in practice?  → Quick Sort (with random pivot)
Sorting integers with small range? → Radix Sort or Counting Sort
Sorting linked list?               → Merge Sort
Need stable sort?                  → Merge Sort, Insertion Sort, Radix Sort
```

---

## ⚠️ Common Pitfalls

1. **Quick sort on sorted input** — always use random pivot or median-of-three in production.
2. **Merge sort auxiliary array** — allocating inside the merge function on every call is inefficient; allocate once upfront.
3. **Stability assumptions** — unstable sorts cannot safely sort by multiple keys sequentially.
4. **Off-by-one in partition** — the boundary indices `l`, `m`, `r` must be consistent throughout.

---

## 💡 Practice Problems

1. Sort an array of 0s, 1s, and 2s without extra space (Dutch National Flag).
2. Sort a nearly sorted array of n elements where each element is at most k positions away.
3. Implement stable in-place sort.
4. Sort a linked list using merge sort.
5. Find the k-th largest element using a partial sort.

---

## 📚 Further Reading

- CLRS Chapters 6 (Heap Sort), 7 (Quick Sort), 8 (Linear Sorting)
- Knuth, *TAOCP* Vol. 3 — Sorting and Searching
- [Sorting visualizations](https://visualgo.net/en/sorting)
