# 02 — Recursion

> **Files in project:** `Recursion/factorial.c` · `Recursion/factorial.py` · `Recursion/fibo.c` · `Recursion/fibo.py` · `Recursion/GCD.c` · `Recursion/GCD.py` · `Recursion/TowerOfHanoi.c` · `Recursion/TowerofHanoi.py` · `Recursion/SumOfNatural.c` · `Recursion/SumofN.py`

---

## 🧠 Concept Overview

**Recursion** is a technique where a function solves a problem by calling itself on a smaller instance of the same problem. Every recursive solution has two essential parts:

- **Base case** — a condition where the function returns a direct answer (no further calls).
- **Recursive case** — the function calls itself with a simpler or smaller input, moving toward the base case.

Recursion is not just a coding trick — it is a mathematical principle (induction) that maps directly onto many algorithmic problems including tree traversal, divide-and-conquer, and backtracking.

---

## 📖 Key Terminology

| Term | Meaning |
|------|---------|
| Base Case | The stopping condition that terminates recursion |
| Recursive Case | The self-referential call that reduces the problem |
| Call Stack | Memory region storing each function's frame during recursion |
| Stack Frame | Local variables + return address for a single function call |
| Stack Overflow | Infinite recursion consumes all call-stack memory |
| Tail Recursion | The recursive call is the very last operation — optimizable |
| Memoization | Caching results of recursive calls to avoid recomputation |
| Recurrence Relation | Mathematical equation that defines T(n) in terms of T(smaller) |

---

## 🔧 Problems Implemented

### 1. Factorial (`factorial.c`)

**Definition:** `n! = n × (n−1) × ... × 1`, with `0! = 1` and `1! = 1`

The project uses **tail recursion** — the recursive call is the last operation:

```c
long int tailFact(int n, long int a) {
    if (n == 1) return a;           // base case
    return tailFact(n-1, a*n);      // tail call: result passed as accumulator
}
// called as: tailFact(n, 1)
```

**Why tail recursion?** In tail position, the compiler can reuse the current stack frame instead of creating a new one — effectively converting recursion to iteration. This avoids stack overflow for large `n`.

**Recurrence:** `T(n) = T(n−1) + O(1)` → **O(n)**

---

### 2. Fibonacci (`fibo.c`)

**Definition:** `F(1) = F(2) = 1`, `F(n) = F(n−1) + F(n−2)` for n > 2

The project uses **memoization** to avoid redundant computation:

```c
long int table[MAX] = {0};   // memo table, 0 means "not computed"

long int Fibo(int n) {
    if (n == 1 || n == 2) return 1;      // base case
    if (table[n] == 0) {                  // not cached yet
        table[n] = Fibo(n-1) + Fibo(n-2);
    }
    return table[n];
}
```

**Without memoization**, naive Fibonacci has complexity **O(2ⁿ)** — it recomputes the same subproblems exponentially many times. **With memoization**, each F(k) is computed exactly once → **O(n)** time, **O(n)** space.

| Approach | Time | Space |
|----------|------|-------|
| Naive recursive | O(2ⁿ) | O(n) stack |
| Memoized (this project) | O(n) | O(n) |
| Bottom-up DP | O(n) | O(n) or O(1) |

---

### 3. GCD — Euclidean Algorithm (`GCD.c`)

**Definition:** GCD(a, b) = largest integer dividing both a and b.

**Euclidean insight:** GCD(a, b) = GCD(b, a mod b), with GCD(a, 0) = a.

```c
long int GCD(int a, int b) {
    if (b == 0) return a;       // base case: b divides a evenly
    return GCD(b, a % b);       // reduce: new pair is (b, remainder)
}
```

**Trace:** GCD(48, 18)
- GCD(48, 18) → GCD(18, 12) → GCD(12, 6) → GCD(6, 0) → **6** ✅

**Complexity:** O(log(min(a,b))) — the values shrink by at least half every two steps (Lamé's theorem).

---

### 4. Tower of Hanoi (`TowerOfHanoi.c`)

**Problem:** Move n discs from peg A to peg C using peg B as auxiliary, without placing a larger disc on a smaller one.

```c
void TOH(int n, char src, char dest, char tmp) {
    if (n == 1) {
        printf("Move disc %d from %c to %c\n", n, src, dest);
    } else {
        TOH(n-1, src, tmp, dest);   // move n-1 discs out of the way
        printf("Move disc %d from %c to %c\n", n, src, dest);
        TOH(n-1, tmp, dest, src);   // put n-1 discs onto dest
    }
}
```

**Why it works (induction):**
- Base: 1 disc → move directly. ✅
- Inductive: Assume we can solve for n−1 discs. For n discs: move top n−1 to aux, move disc n to dest, move n−1 from aux to dest. ✅

**Recurrence:** `T(n) = 2T(n−1) + 1` → **T(n) = 2ⁿ − 1**

This is provably optimal — you cannot solve Tower of Hanoi in fewer than 2ⁿ − 1 moves.

| n | Moves |
|---|-------|
| 1 | 1 |
| 3 | 7 |
| 10 | 1,023 |
| 20 | 1,048,575 |
| 64 | ~1.8 × 10¹⁹ (centuries at 1 move/sec) |

---

### 5. Sum of Natural Numbers (`SumOfNatural.c`)

```c
// Standard form: Sum(n) = n + Sum(n-1), Sum(0) = 0
// Alternatively, closed form: n*(n+1)/2
```

Straightforward linear recursion. Its main value is as a teaching example for how the call stack unwinds.

---

## 📐 Complexity Summary

| Problem | Time | Space (Stack) |
|---------|------|---------------|
| Factorial (tail) | O(n) | O(1) with TCO, O(n) without |
| Fibonacci (naive) | O(2ⁿ) | O(n) |
| Fibonacci (memo) | O(n) | O(n) |
| GCD | O(log min(a,b)) | O(log min(a,b)) |
| Tower of Hanoi | O(2ⁿ) | O(n) |
| Sum of N | O(n) | O(n) |

---

## 🔄 Recursion vs Iteration

| Criterion | Recursion | Iteration |
|-----------|-----------|-----------|
| Readability | Often cleaner for tree/divide problems | Cleaner for simple loops |
| Stack usage | Implicit call stack — overflow risk | Explicit loop — O(1) extra |
| Performance | Function call overhead | Usually faster |
| Tail recursion | Compiler may optimize to iteration | Already iterative |

**Rule of thumb:** Use recursion when the problem is naturally self-similar (trees, divide-and-conquer, backtracking). Convert to iteration when depth is large and tail-call optimization is unavailable.

---

## 🌳 Recursion Tree Visualization

For `Fibo(5)` (without memoization):

```
             Fibo(5)
           /         \
        Fibo(4)      Fibo(3)
        /    \        /   \
    Fibo(3) Fibo(2) Fibo(2) Fibo(1)
    /   \
Fibo(2) Fibo(1)
```

Notice `Fibo(3)` is computed twice, `Fibo(2)` three times — this is the exponential blowup memoization prevents.

---

## ⚠️ Common Pitfalls

1. **Missing base case** → infinite recursion → stack overflow.
2. **Base case unreachable** → ensure the recursive call strictly moves *toward* the base case.
3. **Integer overflow** → factorial grows extremely fast; use `long long` or arbitrary precision.
4. **Redundant recomputation** → always consider memoization for overlapping subproblems.

---

## 💡 Practice Problems

1. Write recursive power function: `pow(base, exp)`.
2. Check if a string is a palindrome recursively.
3. Flatten a nested list recursively.
4. Generate all permutations of a string.
5. Solve the N-Queens problem (see Topic 12 — Backtracking).
6. Implement merge sort recursively (see Topic 04).

---

## 📚 Further Reading

- CLRS Chapter 4 (Divide and Conquer) and Appendix B (Recurrences)
- *Structure and Interpretation of Computer Programs* (SICP) — Chapter 1
- [Visualize recursion](https://pythontutor.com) — step through call stacks online
