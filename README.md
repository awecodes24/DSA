# 📚 Data Structures & Algorithms — Complete Study Guide

> A comprehensive topic-by-topic reference covering every major area of DSA, with implementation notes drawn from this project's C and Python source files, plus full conceptual coverage of topics not yet implemented.

---

## 📂 Project Structure

```
DSA-main/
├── Introduction/       → Stack as ADT, Postfix Evaluation
├── Recursion/          → Factorial, Fibonacci, GCD, Tower of Hanoi, Sum of N
├── Linked list/        → SLL, DLL, Stack & Queue via SLL, Reverse SLL/DLL
├── Sorting/            → Bubble, Selection, Insertion, Shell, Merge, Quick, Heap, Radix
├── BinaryTree/         → BST (C), AVL Tree (Python)
├── spanning tree/      → BFS, DFS, Dijkstra, Kruskal, Prim, Floyd-Warshall
├── dijkstra.ipynb      → Dijkstra Jupyter Notebook
└── practice/           → Selection sort practice
```

---

## 🗂️ Topic Index

| # | Topic | Languages Used | Status |
|---|-------|---------------|--------|
| 01 | [Introduction — ADT & Stack](./01-Introduction/README.md) | C, Python | ✅ Implemented |
| 02 | [Recursion](./02-Recursion/README.md) | C, Python | ✅ Implemented |
| 03 | [Linked Lists](./03-LinkedList/README.md) | C | ✅ Implemented |
| 04 | [Sorting Algorithms](./04-Sorting/README.md) | C, Python | ✅ Implemented |
| 05 | [Binary Trees & BST / AVL](./05-BinaryTree/README.md) | C, Python | ✅ Implemented |
| 06 | [Graphs — BFS, DFS, Shortest Path, MST](./06-Graphs/README.md) | Python | ✅ Implemented |
| 07 | [Searching Algorithms](./07-Searching/README.md) | — | 📖 Study Guide |
| 08 | [Hashing](./08-Hashing/README.md) | — | 📖 Study Guide |
| 09 | [Heaps & Priority Queues](./09-Heaps/README.md) | C | ✅ Implemented (Heap Sort) |
| 10 | [Dynamic Programming](./10-DynamicProgramming/README.md) | C, Python | ✅ Partial (Memoized Fib, FW) |
| 11 | [Greedy Algorithms](./11-Greedy/README.md) | Python | ✅ Partial (Kruskal, Prim, Dijkstra) |
| 12 | [Backtracking](./12-Backtracking/README.md) | — | 📖 Study Guide |
| 13 | [Queues](./13-Queues/README.md) | C | ✅ Implemented |
| 14 | [Stacks](./14-Stacks/README.md) | C | ✅ Implemented |

---

## ⏱️ Big-O Quick Reference

| Algorithm | Best | Average | Worst | Space |
|-----------|------|---------|-------|-------|
| Bubble Sort | O(n) | O(n²) | O(n²) | O(1) |
| Selection Sort | O(n²) | O(n²) | O(n²) | O(1) |
| Insertion Sort | O(n) | O(n²) | O(n²) | O(1) |
| Shell Sort | O(n log n) | O(n log² n) | O(n²) | O(1) |
| Merge Sort | O(n log n) | O(n log n) | O(n log n) | O(n) |
| Quick Sort | O(n log n) | O(n log n) | O(n²) | O(log n) |
| Heap Sort | O(n log n) | O(n log n) | O(n log n) | O(1) |
| Radix Sort | O(nk) | O(nk) | O(nk) | O(n+k) |
| Binary Search | O(1) | O(log n) | O(log n) | O(1) |
| BFS/DFS | O(V+E) | O(V+E) | O(V+E) | O(V) |
| Dijkstra (PQ) | O((V+E)log V) | O((V+E)log V) | O((V+E)log V) | O(V) |
| Kruskal | O(E log E) | O(E log E) | O(E log E) | O(V) |
| Prim | O(E log V) | O(E log V) | O(E log V) | O(V) |

---

## 🔗 How to Use These READMEs

Each topic README contains:
- **Concept Overview** — what it is and why it matters
- **Key Terminology** — vocabulary you must know
- **How It Works** — step-by-step explanation
- **Complexity Analysis** — time and space
- **Code Walkthrough** — annotated snippets from the project
- **Common Pitfalls** — mistakes to avoid
- **Practice Problems** — curated exercises
- **Further Reading** — references

---

*Languages: C · Python | This project is a study collection — not a production library.*
