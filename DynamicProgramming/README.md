# 10 — Dynamic Programming

> **Files in project:** `Recursion/fibo.c` (memoized Fibonacci) · `spanning tree/FloydWarshall.py` (DP on all-pairs shortest paths)  
> **Status:** DP concepts partially implemented; full study guide

---

## 🧠 Concept Overview

**Dynamic Programming (DP)** is a problem-solving paradigm that solves complex problems by breaking them into simpler **overlapping subproblems**, solving each subproblem once, and storing the results to avoid redundant computation.

DP applies when a problem has two properties:
1. **Optimal Substructure** — an optimal solution can be built from optimal solutions of its subproblems.
2. **Overlapping Subproblems** — the same subproblems appear multiple times.

DP is **not a data structure** — it is a technique. It sits between "brute force recursion" and "greedy" on the algorithm design spectrum.

---

## 📖 Key Terminology

| Term | Meaning |
|------|---------|
| Subproblem | A smaller instance of the original problem |
| Overlapping subproblems | Same subproblem computed multiple times in naive recursion |
| Optimal substructure | Optimal solution contains optimal sub-solutions |
| Memoization | Top-down DP: cache recursive results |
| Tabulation | Bottom-up DP: fill a table iteratively |
| State | The set of parameters that uniquely identify a subproblem |
| Transition | How to compute a state from previous states |
| Base Case | Initial state(s) with known values |
| DP Table | Array or dict storing computed subproblem results |

---

## 🔧 Two Approaches

### Top-Down (Memoization) — used in `fibo.c`

```c
long int table[MAX] = {0};   // 0 = "not computed"

long int Fibo(int n) {
    if (n == 1 || n == 2) return 1;
    if (table[n] == 0)
        table[n] = Fibo(n-1) + Fibo(n-2);   // compute and cache
    return table[n];
}
```

**Pattern:** Write the recursive solution, then add a cache. Call recursively but return cached value if already computed.

### Bottom-Up (Tabulation)

```python
def fib(n):
    if n <= 2: return 1
    dp = [0] * (n + 1)
    dp[1] = dp[2] = 1
    for i in range(3, n + 1):
        dp[i] = dp[i-1] + dp[i-2]
    return dp[n]
```

**Pattern:** Determine the order in which subproblems must be solved, fill the table from smallest to largest.

| Criterion | Memoization | Tabulation |
|-----------|-------------|------------|
| Code style | Recursive — natural | Iterative — explicit |
| Stack usage | O(n) call stack | O(1) extra |
| Subproblem order | Automatic | Must determine |
| Only needed subproblems | ✅ Yes | ❌ Computes all |

---

## 🔧 Classic DP Problems

---

### 1. Fibonacci (project: `fibo.c`)

Already covered in Topic 02. DP insight: `F(n) = F(n-1) + F(n-2)`.

**Space optimization:** You only need the last two values:
```python
a, b = 1, 1
for _ in range(n - 2):
    a, b = b, a + b
return b   # O(1) space
```

---

### 2. 0/1 Knapsack

**Problem:** n items, each with weight wᵢ and value vᵢ. Knapsack capacity W. Maximize total value without exceeding capacity.

**State:** `dp[i][w]` = max value using first i items with capacity w.

**Transition:**
```
dp[i][w] = max(
    dp[i-1][w],                       # don't take item i
    dp[i-1][w - w[i]] + v[i]          # take item i (if w[i] <= w)
)
```

```python
def knapsack(weights, values, W):
    n = len(weights)
    dp = [[0] * (W + 1) for _ in range(n + 1)]
    for i in range(1, n + 1):
        for w in range(W + 1):
            dp[i][w] = dp[i-1][w]
            if weights[i-1] <= w:
                dp[i][w] = max(dp[i][w], dp[i-1][w - weights[i-1]] + values[i-1])
    return dp[n][W]
```

Time: O(nW), Space: O(nW). Can reduce space to O(W) by using 1D table (iterate w in reverse).

---

### 3. Longest Common Subsequence (LCS)

**Problem:** Given strings X and Y, find the longest subsequence present in both.

**State:** `dp[i][j]` = LCS length of X[0..i-1] and Y[0..j-1].

```python
def lcs(X, Y):
    m, n = len(X), len(Y)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if X[i-1] == Y[j-1]:
                dp[i][j] = dp[i-1][j-1] + 1
            else:
                dp[i][j] = max(dp[i-1][j], dp[i][j-1])
    return dp[m][n]
```

Time: O(mn), Space: O(mn).

---

### 4. Floyd-Warshall (project: `FloydWarshall.py`)

All-pairs shortest paths. DP on intermediate vertices:

```python
# D[k][i][j] = shortest path from i to j using only vertices {0..k} as intermediates
D[k+1][i][j] = min(D[k][i][j], D[k][i][k] + D[k][k][j])
```

This is the classic DP pattern applied to graphs. See Topic 06 for full details.

---

### 5. Longest Increasing Subsequence (LIS)

**State:** `dp[i]` = length of LIS ending at index i.

```python
def lis(arr):
    n = len(arr)
    dp = [1] * n
    for i in range(1, n):
        for j in range(i):
            if arr[j] < arr[i]:
                dp[i] = max(dp[i], dp[j] + 1)
    return max(dp)
```

Time: O(n²). Can be improved to O(n log n) with binary search.

---

### 6. Coin Change (Minimum Coins)

**Problem:** Given coins of denominations `coins[]` and target amount `amount`, find minimum number of coins.

```python
def coin_change(coins, amount):
    dp = [float('inf')] * (amount + 1)
    dp[0] = 0
    for coin in coins:
        for x in range(coin, amount + 1):
            dp[x] = min(dp[x], dp[x - coin] + 1)
    return dp[amount] if dp[amount] != float('inf') else -1
```

---

### 7. Matrix Chain Multiplication

**Problem:** Find the most efficient way to multiply a chain of matrices.

**State:** `dp[i][j]` = minimum multiplications for matrices from i to j.

```python
dp[i][j] = min over all k of: dp[i][k] + dp[k+1][j] + p[i-1]*p[k]*p[j]
```

Classic example of **interval DP** — subproblems are defined by intervals.

---

## 📐 DP Problem Recognition Guide

```
Does the problem ask for:
    → Count of ways?        → Usually DP
    → Maximum/Minimum?      → Usually DP or Greedy
    → Yes/No feasibility?   → Sometimes DP

Does it have:
    → Choices at each step?         → DP
    → Subproblems that overlap?     → DP (not just divide-and-conquer)
    → A natural ordering (index,
      length, position)?            → Tabulate in that order
```

---

## 📐 Complexity Patterns

| Problem Type | States | Transition | Total |
|-------------|--------|-----------|-------|
| 1D DP (Fib, LIS) | O(n) | O(1) or O(n) | O(n) or O(n²) |
| 2D DP (LCS, Knapsack) | O(mn) | O(1) | O(mn) |
| Interval DP (MCM) | O(n²) | O(n) | O(n³) |
| Tree DP | O(n) | O(children) | O(n) |
| Bitmask DP (TSP) | O(2ⁿ·n) | O(n) | O(2ⁿ·n²) |

---

## ⚠️ Common Pitfalls

1. **Incorrect state definition** — the state must capture everything needed to make future decisions.
2. **Wrong transition order** — in bottom-up, ensure all dependencies are computed before the current cell.
3. **Base case errors** — off-by-one in initializing dp[0] vs dp[1].
4. **Greedy ≠ DP** — greedy makes a local optimal choice; DP considers all options. Coin change with arbitrary denominations requires DP, not greedy.

---

## 💡 Practice Problems (Increasing Difficulty)

1. Climbing stairs — how many ways to reach step n?
2. House robber — maximize loot without robbing adjacent houses.
3. Unique paths in a grid.
4. Edit distance (Levenshtein distance).
5. Word break problem.
6. Burst balloons.
7. Traveling Salesman Problem with bitmask DP.

---

## 📚 Further Reading

- CLRS Chapters 14–15 (Dynamic Programming)
- *Competitive Programmer's Handbook* — Chapter 7
- [DP Patterns on LeetCode](https://leetcode.com/discuss/study-guide/458695/)
