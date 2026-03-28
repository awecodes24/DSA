# 14 — Stacks

> **Files in project:** `Introduction/stackasADT.c` · `Introduction/postfix.c` · `Introduction/postfix.py` · `Linked list/StackUsingSLL.c`  
> **Status:** Fully implemented (array-based and linked-list-based)

---

## 🧠 Concept Overview

A **Stack** is a linear data structure that follows the **Last In, First Out (LIFO)** principle. The most recently added element is the first one to be removed — like a stack of plates.

Stacks are one of the most fundamental and widely used data structures, appearing in function call management, expression evaluation, undo/redo systems, and depth-first search.

---

## 📖 Key Terminology

| Term | Meaning |
|------|---------|
| LIFO | Last In, First Out — defining property of a stack |
| Push | Add element to the top |
| Pop | Remove and return the top element |
| Peek / Top | Read the top element without removing |
| Empty | Stack has no elements |
| Overflow | Pushing onto a full stack (array-based) |
| Underflow | Popping from an empty stack |

---

## 🔧 Implementations

### 1. Array-Based Stack (`stackasADT.c`)

```c
typedef struct {
    int value[MAX];
    int TOS;          // Top of Stack index; -1 = empty
} Stack;

void Push(Stack *s, int value) {
    if (s->TOS == MAX - 1) { printf("Overflow\n"); return; }
    s->value[++(s->TOS)] = value;
}

int Pop(Stack *s) {
    if (s->TOS == -1) { printf("Underflow\n"); exit(1); }
    return s->value[(s->TOS)--];
}

int Peek(Stack *s) {
    if (s->TOS == -1) { printf("Empty\n"); exit(1); }
    return s->value[s->TOS];
}
```

**Pros:** Fast (cache-friendly), simple.  
**Cons:** Fixed maximum size.

### 2. Linked-List Stack (`StackUsingSLL.c`)

```c
struct DynamicStack {
    int data;
    struct DynamicStack *next;
};
struct DynamicStack *top = NULL;

// Push = insert at head (O(1))
// Pop  = delete from head (O(1))
```

**Pros:** No size limit, grows dynamically.  
**Cons:** Extra memory per node (pointer), heap allocation overhead.

---

## 🔧 Applications

### Expression Evaluation (`postfix.c`)

Postfix (RPN) evaluation using a stack — see Topic 01 for full details.

```
Expression: "3 4 + 5 *"
Result: 35
```

### Bracket Matching

```python
def is_balanced(s):
    stack = []
    pairs = {')': '(', '}': '{', ']': '['}
    for c in s:
        if c in '({[':
            stack.append(c)
        elif c in ')}]':
            if not stack or stack[-1] != pairs[c]:
                return False
            stack.pop()
    return len(stack) == 0
```

### DFS (Explicit Stack)

Graph DFS using an explicit stack instead of recursion — avoids call-stack overflow on deep graphs. See Topic 06.

### Undo / Redo

```python
undo_stack = []
redo_stack = []

def do_action(action):
    undo_stack.append(action)
    redo_stack.clear()

def undo():
    if undo_stack:
        redo_stack.append(undo_stack.pop())

def redo():
    if redo_stack:
        undo_stack.append(redo_stack.pop())
```

### Function Call Stack

The OS/runtime uses an implicit stack for function calls. Each `call` pushes a frame (local vars + return address); each `return` pops it. Recursive overflow = stack overflow.

---

## 📐 Complexity

| Operation | Array Stack | Linked Stack |
|-----------|-------------|-------------|
| Push | O(1) | O(1) |
| Pop | O(1) | O(1) |
| Peek | O(1) | O(1) |
| IsEmpty | O(1) | O(1) |
| Space | O(n) fixed | O(n) dynamic |

---

## 🎯 Min Stack (Classic Interview Problem)

Design a stack that supports `push`, `pop`, `peek`, and `getMin` all in O(1):

```python
class MinStack:
    def __init__(self):
        self.stack = []
        self.min_stack = []   # parallel stack tracking minimums

    def push(self, val):
        self.stack.append(val)
        min_val = min(val, self.min_stack[-1] if self.min_stack else val)
        self.min_stack.append(min_val)

    def pop(self):
        self.stack.pop()
        self.min_stack.pop()

    def get_min(self):
        return self.min_stack[-1]
```

---

## ⚠️ Common Pitfalls

1. **Array overflow** — always check `IsFull` before pushing in fixed-size stacks.
2. **Empty stack pop** — always check `IsEmpty` before popping.
3. **Dangling top pointer** — in linked-list stack, after pop, free the old top before updating `top`.
4. **Recursion = hidden stack** — every recursive call uses the system stack; very deep recursion needs explicit stack iteration.

---

## 💡 Practice Problems

1. Evaluate postfix expression.
2. Convert infix to postfix (Shunting Yard).
3. Next Greater Element for each array element.
4. Largest rectangle in histogram.
5. Implement a stack using two queues.
6. Simplify a Unix file path.

---

## 📚 Further Reading

- CLRS Chapter 10.1 — Stacks and Queues
- See Topic 01 (Introduction) for stack as ADT
- See Topic 13 (Queues) for the complementary FIFO structure
