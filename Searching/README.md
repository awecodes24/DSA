# 07 — Searching Algorithms

> **Files in project:** _(not directly implemented as standalone files — search is embedded in BST.c and linked list operations)_  
> **Status:** Full study guide

---

## 🧠 Concept Overview

**Searching** is the process of finding a target value within a data structure. The efficiency of search depends critically on whether the data is **sorted** and how it is **structured**. This topic covers the classical searching algorithms every DSA student must know.

---

## 📖 Key Terminology

| Term | Meaning |
|------|---------|
| Linear Search | Check every element in sequence |
| Binary Search | Halve the search space at each step (requires sorted input) |
| Sentinel Search | Place target at end to eliminate boundary check |
| Interpolation Search | Estimate position based on value distribution |
| Exponential Search | Find range, then binary search within it |
| Jump Search | Skip ahead in blocks, then scan backward |

---

## 🔧 Algorithms

---

### 1. Linear Search

**Idea:** Examine each element from left to right until the target is found or the array is exhausted.

```python
def linear_search(arr, target):
    for i in range(len(arr)):
        if arr[i] == target:
            return i    # found at index i
    return -1           # not found
```

| Property | Value |
|----------|-------|
| Best case | O(1) — target at first position |
| Average | O(n/2) ≈ O(n) |
| Worst case | O(n) — target at last position or absent |
| Space | O(1) |
| Requires sorted | ❌ No |

**Sentinel optimization:** Place the target at position n, eliminating the `i < n` check each iteration. On large arrays, this removes ~half the comparisons.

---

### 2. Binary Search

**Idea:** On a sorted array, compare the target with the middle element. If smaller, search the left half; if larger, search the right half. Repeat.

```python
def binary_search(arr, target):
    low, high = 0, len(arr) - 1
    while low <= high:
        mid = (low + high) // 2          # avoid overflow: low + (high-low)//2
        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            low = mid + 1                # target in right half
        else:
            high = mid - 1               # target in left half
    return -1
```

**Trace:** Search for 13 in `[1, 3, 4, 6, 7, 8, 10, 13, 14]`

| Step | low | high | mid | arr[mid] | Action |
|------|-----|------|-----|----------|--------|
| 1 | 0 | 8 | 4 | 7 | 13 > 7, low = 5 |
| 2 | 5 | 8 | 6 | 10 | 13 > 10, low = 7 |
| 3 | 7 | 8 | 7 | 13 | Found! |

**Recursive version:**
```python
def binary_search_recursive(arr, target, low, high):
    if low > high: return -1
    mid = (low + high) // 2
    if arr[mid] == target: return mid
    elif arr[mid] < target: return binary_search_recursive(arr, target, mid+1, high)
    else: return binary_search_recursive(arr, target, low, mid-1)
```

| Property | Value |
|----------|-------|
| Best case | O(1) |
| Average/Worst | **O(log n)** |
| Space (iterative) | O(1) |
| Space (recursive) | O(log n) stack |
| Requires sorted | ✅ Yes |

**⚠️ Integer overflow:** In C/Java, `(low + high) / 2` can overflow when both are large. Always use `low + (high - low) / 2`.

---

### 3. Interpolation Search

**Idea:** Instead of always checking the midpoint, estimate where the target likely is based on its value relative to the min and max.

```python
def interpolation_search(arr, target):
    low, high = 0, len(arr) - 1
    while low <= high and arr[low] <= target <= arr[high]:
        # Estimate position by linear interpolation
        pos = low + ((target - arr[low]) * (high - low) // (arr[high] - arr[low]))
        if arr[pos] == target:
            return pos
        elif arr[pos] < target:
            low = pos + 1
        else:
            high = pos - 1
    return -1
```

| Property | Value |
|----------|-------|
| Average (uniform distribution) | **O(log log n)** |
| Worst case | O(n) — non-uniform data |
| Requires sorted | ✅ Yes |

Best for **uniformly distributed sorted data** (e.g., a sorted list of phone numbers).

---

### 4. Jump Search

**Idea:** Jump ahead by √n steps; when you overshoot, do linear search backward within the block.

```python
import math

def jump_search(arr, target):
    n = len(arr)
    step = int(math.sqrt(n))
    prev = 0
    while arr[min(step, n) - 1] < target:
        prev = step
        step += int(math.sqrt(n))
        if prev >= n: return -1
    while arr[prev] < target:
        prev += 1
        if prev == min(step, n): return -1
    if arr[prev] == target: return prev
    return -1
```

| Property | Value |
|----------|-------|
| Time | O(√n) |
| Optimal block size | √n |
| Requires sorted | ✅ Yes |

Useful when **backward jumps are expensive** (e.g., sequential-read media).

---

### 5. Exponential Search

**Idea:** Find the range where the element exists (doubling the range each time), then binary search within it.

```python
def exponential_search(arr, target):
    if arr[0] == target: return 0
    i = 1
    while i < len(arr) and arr[i] <= target:
        i *= 2
    return binary_search(arr[i//2 : min(i, len(arr))], target)
```

| Property | Value |
|----------|-------|
| Time | O(log n) |
| Best for | Unbounded/infinite sorted arrays |
| Requires sorted | ✅ Yes |

---

## 📐 Comparison Summary

| Algorithm | Best | Average | Worst | Sorted Required |
|-----------|------|---------|-------|-----------------|
| Linear | O(1) | O(n) | O(n) | ❌ |
| Binary | O(1) | O(log n) | O(log n) | ✅ |
| Interpolation | O(1) | O(log log n) | O(n) | ✅ |
| Jump | O(1) | O(√n) | O(√n) | ✅ |
| Exponential | O(1) | O(log n) | O(log n) | ✅ |
| BST Search | O(1) | O(log n) | O(n) | N/A (structure) |

---

## 🔍 Search in This Project

Although there is no standalone search file, search appears throughout:

- **SLL (`linkedList.c`):** Linear search — scans from `first` to `NULL`.
- **DLL (`doublyLinkedList.c`):** Linear search — same approach.
- **BST (`BST.c`):** Binary search on tree structure — O(log n) average.

---

## ⚠️ Common Pitfalls

1. **Binary search on unsorted array** — gives incorrect results with no error.
2. **Off-by-one bounds** — `while low <= high` vs `while low < high` depends on whether `high` is inclusive.
3. **Integer overflow in mid calculation** — use `low + (high-low)//2`.
4. **Interpolation on non-uniform data** — degrades to O(n); never assume uniform distribution without evidence.

---

## 💡 Practice Problems

1. Find the first and last occurrence of a target in a sorted array.
2. Search in a rotated sorted array.
3. Find the square root of a number using binary search.
4. Find a peak element in an array.
5. Binary search on answer — minimize the maximum subarray sum.
6. Count occurrences of a target in a sorted array.

---

## 📚 Further Reading

- CLRS Chapter 2.1 — Insertion Sort introduces linear search concepts
- Knuth, TAOCP Vol. 3 Section 6.1 — Sequential Searching
- [Binary Search Problems on LeetCode](https://leetcode.com/tag/binary-search/)
