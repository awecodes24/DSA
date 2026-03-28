# 03 — Linked Lists

> **Files in project:** `Linked list/linkedList.c` · `Linked list/doublyLinkedList.c` · `Linked list/StackUsingSLL.c` · `Linked list/QueueUsingSLL.c` · `Linked list/ReverseOfSLL.c` · `Linked list/ReverseOfDLL.c`

---

## 🧠 Concept Overview

A **linked list** is a linear data structure where each element (node) stores its value plus a pointer to the next node. Unlike arrays, nodes are not contiguous in memory — the structure is defined entirely by the pointer chain.

This design trade-off gives linked lists **O(1) insertion/deletion** at known positions but sacrifices **O(1) random access** (arrays have it; linked lists don't).

---

## 📖 Key Terminology

| Term | Meaning |
|------|---------|
| Node | A unit containing data + pointer(s) |
| Head / First | Pointer to the first node |
| Tail / Last | Pointer to the last node |
| `next` | Forward pointer in SLL/DLL |
| `prev` | Backward pointer in DLL |
| NULL | Marks the end of the list |
| Sentinel | Dummy node to simplify edge cases |
| SLL | Singly Linked List — one `next` pointer per node |
| DLL | Doubly Linked List — both `next` and `prev` pointers |
| CLL | Circular Linked List — last node's `next` points to head |

---

## 🔧 Types Implemented

### 1. Singly Linked List (SLL) — `linkedList.c`

**Node structure:**
```c
struct SLL {
    int data;
    struct SLL *next;
};
struct SLL *first = NULL, *last = NULL;
```

**Memory layout:**
```
first                          last
  │                              │
  ▼                              ▼
[10|•]──►[20|•]──►[30|•]──►[40|NULL]
```

**Operations implemented:**

| Operation | Location | Complexity |
|-----------|----------|-----------|
| Insert at beginning | `InsertionFromBeg` | O(1) |
| Insert at end | `InsertionFromEnd` | O(1) — uses `last` pointer |
| Insert at position k | `InsertionFromPOS` | O(k) |
| Delete from beginning | `DeletionFromBeg` | O(1) |
| Delete from end | `DeletionFromEnd` | O(n) — must find second-to-last |
| Delete at position k | `DeletionFromPOS` | O(k) |
| Search | `Search` | O(n) |
| Traverse | `Traverse` | O(n) |

**Insert at beginning — annotated:**
```c
void InsertionFromBeg(int value) {
    struct SLL *NewNode = malloc(sizeof(struct SLL));
    NewNode->data = value;
    NewNode->next = NULL;

    if (first == NULL)               // empty list — new node is both first and last
        first = last = NewNode;
    else {
        NewNode->next = first;       // new node points to old head
        first = NewNode;             // head moves to new node
    }
}
```

---

### 2. Doubly Linked List (DLL) — `doublyLinkedList.c`

**Node structure:**
```c
struct DLL {
    int data;
    struct DLL *prev;
    struct DLL *next;
};
```

**Memory layout:**
```
NULL◄─[10]─►◄─[20]─►◄─[30]─►◄─[40]──►NULL
     first                       last
```

**Advantages over SLL:**
- Delete from end is now O(1) — just follow `last->prev`.
- Traverse in both directions.
- Easier deletion given a pointer to the node.

**Delete from end — DLL vs SLL comparison:**

```c
// SLL: must walk to second-to-last — O(n)
while (temp->next != last) temp = temp->next;

// DLL: last already knows its predecessor — O(1)
last = last->prev;
last->next = NULL;
```

---

### 3. Stack Using SLL — `StackUsingSLL.c`

A **dynamic stack** with no fixed upper limit, built on SLL nodes:

```c
struct DynamicStack {
    int data;
    struct DynamicStack *next;
};
struct DynamicStack *top = NULL;

void PUSH(int element) {
    // allocate new node, set next = top, update top
}
void POP() {
    // save old top, advance top to top->next, free old top
}
```

- **Push = insert at head** (O(1))
- **Pop = delete from head** (O(1))
- Grows dynamically — no overflow as long as memory is available.

---

### 4. Queue Using SLL — `QueueUsingSLL.c`

A **dynamic queue** (FIFO) using two pointers `FRONT` and `REAR`:

```c
struct Queue {
    int data;
    struct Queue *next;
};
struct Queue *FRONT, *REAR = NULL;
```

- **Enqueue = insert at REAR** (O(1))
- **Dequeue = delete from FRONT** (O(1))

```
FRONT                       REAR
  │                           │
  ▼                           ▼
[10]──►[20]──►[30]──►[40]──►[50|NULL]
 ← dequeue here    enqueue here →
```

---

### 5. Reverse of SLL / DLL

**Reverse SLL algorithm (iterative):**
```
prev = NULL
curr = first
while curr != NULL:
    next_node = curr->next
    curr->next = prev      // reverse the pointer
    prev = curr
    curr = next_node
first = prev              // new head
```

**Three-pointer technique:** At each step, you hold `prev`, `curr`, and the saved `next` — because once you reverse `curr->next`, you'd lose the rest of the list.

---

## 📐 Complexity Summary

| Operation | SLL | DLL |
|-----------|-----|-----|
| Access by index | O(n) | O(n) |
| Insert at head | O(1) | O(1) |
| Insert at tail | O(1)† | O(1) |
| Insert at position k | O(k) | O(k) |
| Delete at head | O(1) | O(1) |
| Delete at tail | O(n) | O(1) |
| Delete at position k | O(k) | O(k) |
| Search | O(n) | O(n) |
| Reverse | O(n) | O(n) |

† Requires maintaining a `last` pointer, as done in this project.

---

## 🆚 Array vs Linked List

| Feature | Array | Linked List |
|---------|-------|-------------|
| Memory layout | Contiguous | Scattered (heap) |
| Random access | O(1) | O(n) |
| Insert/delete at front | O(n) shift | O(1) |
| Insert/delete at end | O(1) amortized | O(1) with tail ptr |
| Cache friendliness | High | Low |
| Size flexibility | Fixed (or expensive resize) | Dynamic |
| Extra memory | None | 1–2 pointers per node |

**Use arrays when:** You need frequent random access or the size is known and fixed.  
**Use linked lists when:** You need frequent insertions/deletions at arbitrary positions and rarely access by index.

---

## ⚠️ Common Pitfalls

1. **Memory leaks** — always `free()` deleted nodes.
2. **Dangling pointers** — don't access freed memory; set pointer to `NULL` after freeing.
3. **Losing the head** — always save a reference to `first` before modifying it.
4. **Single-element edge case** — when the list has exactly one node, both `first == last`; handle separately.
5. **NULL dereference** — check `first != NULL` before accessing `first->next`.

---

## 🔗 Circular Linked List (Not in project — study topic)

In a **Circular Linked List**, `last->next` points back to `first` instead of `NULL`.

```
┌──────────────────────────────┐
│                              │
▼                              │
[10]──►[20]──►[30]──►[40]──────┘
  first
```

**Use case:** Round-robin scheduling, circular buffers, music playlists with repeat.

---

## 💡 Practice Problems

1. Detect if a linked list has a cycle (Floyd's cycle detection algorithm).
2. Find the middle node in one pass.
3. Merge two sorted linked lists.
4. Remove the k-th node from the end.
5. Check if a linked list is a palindrome.
6. Flatten a multilevel doubly linked list.

---

## 📚 Further Reading

- CLRS Chapter 10.2 — Linked Lists
- *Data Structures and Algorithm Analysis in C* — Weiss, Chapter 3
