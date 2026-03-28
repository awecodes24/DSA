# 13 — Queues

> **Files in project:** `Linked list/QueueUsingSLL.c`  
> **Status:** Dynamic queue implemented; circular/deque are study guide extensions

---

## 🧠 Concept Overview

A **Queue** is a linear data structure that follows the **First In, First Out (FIFO)** principle. The first element added is the first one removed — like a line at a ticket counter.

Queues are fundamental to scheduling systems, BFS graph traversal, buffering in I/O systems, and message passing in operating systems and distributed systems.

---

## 📖 Key Terminology

| Term | Meaning |
|------|---------|
| FIFO | First In, First Out — defining property of a queue |
| Enqueue | Add element to the rear |
| Dequeue | Remove and return the front element |
| Front | The end from which elements are removed |
| Rear | The end to which elements are added |
| Empty Queue | No elements |
| Overflow | Enqueuing onto a full queue (array-based) |
| Underflow | Dequeuing from an empty queue |
| Circular Queue | Array-based queue that wraps around |
| Deque | Double-ended queue — insert/remove from both ends |
| Priority Queue | Queue where highest-priority element is dequeued first |

---

## 🔧 Implementations

### 1. Linked-List Queue (`QueueUsingSLL.c`)

Two pointers — `FRONT` (dequeue here) and `REAR` (enqueue here):

```c
struct Queue {
    int data;
    struct Queue *next;
};
struct Queue *FRONT, *REAR = NULL;

void Enqueue(int element) {
    struct Queue *NewNode = malloc(sizeof(struct Queue));
    NewNode->data = element;
    NewNode->next = NULL;
    if (FRONT == NULL)
        FRONT = REAR = NewNode;
    else {
        REAR->next = NewNode;
        REAR = NewNode;            // rear advances to new node
    }
}

void Dequeue() {
    if (FRONT == NULL) { printf("Empty Queue!\n"); return; }
    struct Queue *temp = FRONT;
    FRONT = FRONT->next;           // front advances
    if (FRONT == NULL) REAR = NULL;  // queue is now empty
    free(temp);
}
```

**Visual:**
```
FRONT                     REAR
  │                         │
  ▼                         ▼
[10]──►[20]──►[30]──►[40|NULL]
 ← dequeue                 enqueue →
```

**Complexity:** All operations O(1). No fixed size limit.

---

### 2. Array-Based Queue (Linear — Study)

```python
class ArrayQueue:
    def __init__(self, capacity):
        self.data = [None] * capacity
        self.front = 0
        self.rear = -1
        self.size = 0
        self.capacity = capacity

    def enqueue(self, val):
        if self.size == self.capacity:
            raise Exception("Queue overflow")
        self.rear += 1
        self.data[self.rear] = val
        self.size += 1

    def dequeue(self):
        if self.size == 0:
            raise Exception("Queue underflow")
        val = self.data[self.front]
        self.front += 1
        self.size -= 1
        return val
```

**Problem:** `front` drifts rightward after each dequeue; old slots are wasted. After n enqueues and n dequeues, `rear` has hit capacity even though the queue is empty.

**Solution:** Circular Queue.

---

### 3. Circular Queue (Array-Based)

Wrap indices around using modular arithmetic:

```python
class CircularQueue:
    def __init__(self, capacity):
        self.data = [None] * capacity
        self.front = 0
        self.rear = -1
        self.size = 0
        self.capacity = capacity

    def enqueue(self, val):
        if self.size == self.capacity:
            raise Exception("Full")
        self.rear = (self.rear + 1) % self.capacity   # wrap around
        self.data[self.rear] = val
        self.size += 1

    def dequeue(self):
        if self.size == 0:
            raise Exception("Empty")
        val = self.data[self.front]
        self.front = (self.front + 1) % self.capacity  # wrap around
        self.size -= 1
        return val
```

```
capacity = 5, after several enqueue/dequeue:

index:  0    1    2    3    4
data: [20]  [30] [ ]  [ ]  [10]
              ↑               ↑
            rear            front   (wraps!)
```

---

### 4. Deque — Double-Ended Queue (Study)

Supports insert and remove from **both** front and rear in O(1):

```python
from collections import deque
dq = deque()
dq.appendleft(1)   # insert front
dq.append(2)       # insert rear
dq.popleft()       # remove front
dq.pop()           # remove rear
```

**Applications:**
- Sliding window maximum (monotonic deque).
- Palindrome checking.
- BFS with variable-priority insertions.

---

## 🔧 Queue Applications

### BFS — Breadth-First Search

The canonical queue application. See Topic 06:

```python
from queue import Queue
q = Queue()
q.put(start)
while not q.empty():
    u = q.get()
    for v in neighbors(u):
        if v not in visited:
            q.put(v)
```

### CPU / Process Scheduling

Round-robin scheduling: each process gets a time slice, then returns to the rear of the queue.

### Print Spooler / IO Buffer

Jobs enqueued in order; processed in FIFO order.

### Level-Order Tree Traversal

```python
def level_order(root):
    if not root: return []
    result, queue = [], deque([root])
    while queue:
        level = []
        for _ in range(len(queue)):
            node = queue.popleft()
            level.append(node.val)
            if node.left:  queue.append(node.left)
            if node.right: queue.append(node.right)
        result.append(level)
    return result
```

---

## 📐 Complexity Comparison

| Operation | Linked Queue | Circular Array Queue | Python deque |
|-----------|-------------|---------------------|-------------|
| Enqueue | O(1) | O(1) | O(1) |
| Dequeue | O(1) | O(1) | O(1) |
| Peek front | O(1) | O(1) | O(1) |
| IsEmpty | O(1) | O(1) | O(1) |
| Space | O(n) + pointer overhead | O(n) fixed | O(n) |

---

## 🆚 Queue vs Stack vs Deque

| Structure | Order | Add | Remove | Use Case |
|-----------|-------|-----|--------|---------|
| Stack | LIFO | Push (top) | Pop (top) | DFS, undo, recursion |
| Queue | FIFO | Enqueue (rear) | Dequeue (front) | BFS, scheduling |
| Deque | Both | Either end | Either end | Sliding window, palindrome |
| Priority Queue | By priority | Insert | Extract-min/max | Dijkstra, scheduling |

---

## ⚠️ Common Pitfalls

1. **Linear array queue exhaustion** — always use circular queue for fixed arrays.
2. **Empty queue check before dequeue** — always guard with `IsEmpty`.
3. **FRONT/REAR both NULL after last dequeue** — when the last element is dequeued, both pointers must be reset to NULL/invalid.
4. **Confusing queue and stack** — BFS needs a queue; DFS needs a stack (or recursion).

---

## 💡 Practice Problems

1. Implement a queue using two stacks.
2. Implement a stack using two queues.
3. Generate binary numbers 1 to n using a queue.
4. Sliding window maximum using a deque.
5. First non-repeating character in a stream.
6. Rotten oranges — minimum time for all oranges to rot (BFS).

---

## 📚 Further Reading

- CLRS Chapter 10.1 — Stacks and Queues
- Python `collections.deque` documentation
- See Topic 06 (Graphs) for BFS queue usage
- See Topic 09 (Heaps) for Priority Queue
