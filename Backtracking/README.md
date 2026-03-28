# 12 — Backtracking

> **Files in project:** _(not implemented)_  
> **Status:** Full study guide — Tower of Hanoi in Topic 02 shows recursive structure

---

## 🧠 Concept Overview

**Backtracking** is a systematic method for solving constraint satisfaction problems. It builds candidates for solutions incrementally and **abandons ("backtracks")** a candidate as soon as it determines the candidate cannot possibly lead to a valid solution.

Think of it as a **depth-first search on the solution space tree**: explore a branch, if it violates constraints, prune it and try another.

**Backtracking vs Brute Force:** Brute force tries every combination. Backtracking prunes branches early — often drastically reducing the search space.

---

## 📖 Key Terminology

| Term | Meaning |
|------|---------|
| Solution Space | All possible candidates the algorithm might explore |
| State Space Tree | Tree representing all paths of decisions |
| Pruning | Abandoning a branch that can't lead to a solution |
| Promising | A node/state that could lead to a valid solution |
| Constraint | A condition that valid solutions must satisfy |
| Complete Solution | A candidate that satisfies all constraints |
| Partial Solution | A candidate that might be extended to a complete one |

---

## 🔧 General Template

```python
def backtrack(state, choices, result):
    if is_solution(state):         # base case: found valid solution
        result.append(state.copy())
        return

    for choice in choices:
        if is_valid(state, choice):     # pruning condition
            state.add(choice)           # make choice
            backtrack(state, choices, result)
            state.remove(choice)        # undo choice (backtrack)
```

**The three questions to ask:**
1. **When is a state a solution?** → Base case.
2. **What choices are available at each step?** → The loop.
3. **When can a choice be pruned?** → The constraint check.

---

## 🔧 Classic Problems

---

### 1. N-Queens Problem

**Problem:** Place N queens on an N×N chessboard so no two queens threaten each other (no same row, column, or diagonal).

```python
def solve_n_queens(n):
    results = []
    queens = []   # queens[i] = column of queen in row i

    def is_safe(row, col):
        for r, c in enumerate(queens):
            if c == col: return False                 # same column
            if abs(r - row) == abs(c - col): return False  # same diagonal
        return True

    def backtrack(row):
        if row == n:                     # placed all queens
            results.append(queens[:])
            return
        for col in range(n):
            if is_safe(row, col):
                queens.append(col)       # place queen
                backtrack(row + 1)
                queens.pop()             # remove queen (backtrack)

    backtrack(0)
    return results

# For n=4: solutions = [[1,3,0,2], [2,0,3,1]]
```

**State space size without pruning:** N^N  
**With backtracking:** much smaller — for N=8, only 92 solutions among millions of possibilities.

---

### 2. Sudoku Solver

```python
def solve_sudoku(board):
    empty = find_empty(board)
    if not empty: return True   # no empty cells — solved!
    row, col = empty

    for num in range(1, 10):
        if is_valid_sudoku(board, row, col, num):
            board[row][col] = num           # try num
            if solve_sudoku(board): return True
            board[row][col] = 0             # backtrack
    return False
```

---

### 3. Permutations

**Problem:** Generate all permutations of a list.

```python
def permutations(nums):
    result = []
    used = [False] * len(nums)
    current = []

    def backtrack():
        if len(current) == len(nums):
            result.append(current[:])
            return
        for i in range(len(nums)):
            if not used[i]:
                used[i] = True
                current.append(nums[i])
                backtrack()
                current.pop()
                used[i] = False

    backtrack()
    return result
```

Total solutions: N! — no pruning possible when all elements are distinct.

---

### 4. Subsets / Power Set

```python
def subsets(nums):
    result = []
    current = []

    def backtrack(start):
        result.append(current[:])     # every partial state is a valid subset
        for i in range(start, len(nums)):
            current.append(nums[i])
            backtrack(i + 1)
            current.pop()

    backtrack(0)
    return result   # 2^n subsets
```

---

### 5. Word Search in Grid

**Problem:** Given a 2D grid of characters and a word, find if the word exists as a path in the grid.

```python
def word_search(board, word):
    rows, cols = len(board), len(board[0])

    def backtrack(r, c, idx):
        if idx == len(word): return True    # matched whole word
        if r < 0 or r >= rows or c < 0 or c >= cols: return False
        if board[r][c] != word[idx]: return False

        temp = board[r][c]
        board[r][c] = '#'     # mark visited (pruning: don't revisit)

        found = (backtrack(r+1, c, idx+1) or backtrack(r-1, c, idx+1) or
                 backtrack(r, c+1, idx+1) or backtrack(r, c-1, idx+1))

        board[r][c] = temp    # restore (backtrack)
        return found

    for r in range(rows):
        for c in range(cols):
            if backtrack(r, c, 0): return True
    return False
```

---

### 6. Rat in a Maze

**Problem:** Find all paths for a rat to travel from top-left to bottom-right of a maze.

```python
def rat_maze(maze, n):
    solution = [[0]*n for _ in range(n)]
    paths = []

    def backtrack(x, y):
        if x == n-1 and y == n-1:
            paths.append([row[:] for row in solution])
            return
        for dx, dy in [(0,1),(1,0),(0,-1),(-1,0)]:
            nx, ny = x+dx, y+dy
            if 0 <= nx < n and 0 <= ny < n and maze[nx][ny] == 1 and solution[nx][ny] == 0:
                solution[nx][ny] = 1
                backtrack(nx, ny)
                solution[nx][ny] = 0   # backtrack

    if maze[0][0] == 1:
        solution[0][0] = 1
        backtrack(0, 0)
    return paths
```

---

## 📐 Complexity

Backtracking complexity is hard to analyze generally — it depends on the pruning effectiveness:

| Problem | Worst Case | With Good Pruning |
|---------|-----------|------------------|
| N-Queens | O(N^N) | O(N!) or better |
| Permutations | O(N! × N) | O(N! × N) — must output all |
| Subsets | O(2^N × N) | O(2^N × N) — must output all |
| Sudoku | O(9^81) | Far less in practice |
| Word Search | O(N × M × 4^L) | Much less with early exit |

---

## 🆚 Backtracking vs Related Paradigms

| Paradigm | Strategy | Use When |
|----------|---------|---------|
| Brute Force | Try everything | No constraints to prune |
| Backtracking | DFS + pruning | Constraint satisfaction |
| Branch & Bound | Backtracking + optimal bound | Optimization problems |
| DP | Overlapping subproblems | Counting/optimization |
| Greedy | Local optimal choice | Proven greedy property |

---

## ⚠️ Common Pitfalls

1. **Not restoring state** — forgetting to undo the choice after recursion corrupts subsequent branches.
2. **Pruning too late** — check constraints before recursing, not after.
3. **Copying vs referencing** — `result.append(current)` appends a reference that will change; use `current[:]` or `current.copy()`.
4. **Revisiting cells** — in grid problems, mark cells visited before recursing, restore after.

---

## 💡 Practice Problems (Increasing Difficulty)

1. Generate all valid combinations of k numbers from 1 to n.
2. Combination sum — find all combinations that sum to target.
3. Palindrome partitioning of a string.
4. Letter combinations of a phone number.
5. 8-Queens (classic N-Queens with N=8).
6. Cryptarithmetic — SEND + MORE = MONEY.
7. Graph coloring — color a graph with k colors.

---

## 📚 Further Reading

- CLRS Chapter 34 (NP-Completeness context — many NP problems solved via backtracking)
- *Algorithm Design Manual* — Skiena, Chapter 7 (Combinatorial Search)
- Knuth, *The Art of Computer Programming* Vol. 4B — Combinatorial Algorithms
