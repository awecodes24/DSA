# 01 — Introduction: Abstract Data Types & Stack

> **Files in project:** `Introduction/stackasADT.c` · `Introduction/postfix.c` · `Introduction/postfix.py`

---

## 🧠 Concept Overview

Before writing a single line of code for any data structure, you must understand what an **Abstract Data Type (ADT)** is. An ADT defines *what* operations are available on a data type — **not how** those operations are implemented. This separation of interface from implementation is the foundation of good software design.

A **Stack** is the classic introductory ADT. It models a "last-in, first-out" (LIFO) pile: you can only add to and remove from the top.

---

## 📖 Key Terminology

| Term | Meaning |
|------|---------|
| ADT | Abstract Data Type — a mathematical model specifying a data structure's behavior |
| LIFO | Last In, First Out — the access discipline of a stack |
| Push | Add an element to the top of the stack |
| Pop | Remove and return the element at the top |
| Peek / Top | Read the top element without removing it |
| Stack Overflow | Attempting to push onto a full stack |
| Stack Underflow | Attempting to pop from an empty stack |
| Postfix (RPN) | Notation where operators follow their operands: `3 4 +` = 7 |
| Infix | Standard notation where operators sit between operands: `3 + 4` |
| Prefix | Operators precede operands: `+ 3 4` |

---

## 🔧 Stack as ADT — How It Works

The project implements a stack in two flavors:

### Array-based Stack (`stackasADT.c`)

A struct holds the array and a `TOS` (Top of Stack) index:

```c
typedef struct {
    int value[MAX];   // fixed-size storage
    int TOS;          // index of current top (-1 = empty)
} Stack;
```

**Operations and their logic:**

| Operation | Logic | Complexity |
|-----------|-------|-----------|
| `Initialize` | Set `TOS = -1` | O(1) |
| `Push(v)` | Check overflow → increment `TOS` → store value | O(1) |
| `Pop()` | Check underflow → read value → decrement `TOS` | O(1) |
| `Peek()` | Check empty → return `value[TOS]` | O(1) |
| `IsEmpty()` | Return `TOS == -1` | O(1) |
| `IsFull()` | Return `TOS == MAX - 1` | O(1) |

**Why `TOS` starts at -1:** An empty stack has no valid index. `-1` is a sentinel that means "nothing here yet." The first `Push` increments to `0`, placing the element at index 0.

---

## 📝 Postfix Evaluation — How It Works

Postfix evaluation is the canonical stack application. Instead of tracking operator precedence rules, the algorithm is elegant and linear.

**Algorithm:**
1. Scan tokens left to right.
2. If token is a **number** → push it.
3. If token is an **operator** (`+`, `-`, `*`, `/`) → pop two operands, apply the operator, push the result.
4. At the end, the stack holds exactly one value — the answer.

**Example trace:** Expression = `3 4 + 5 *`

| Token | Action | Stack |
|-------|--------|-------|
| `3` | Push 3 | [3] |
| `4` | Push 4 | [3, 4] |
| `+` | Pop 4, Pop 3, push 7 | [7] |
| `5` | Push 5 | [7, 5] |
| `*` | Pop 5, Pop 7, push 35 | [35] |

Result = **35** ✅

**Code from `postfix.c`:**
```c
if (strcmp(character, "+") == 0 || ... ) {
    float operand2 = pop();   // second operand popped first!
    float operand1 = pop();
    result = operand1 + operand2;
    push(result);
} else {
    push(atof(character));    // it's a number — push it
}
```

> ⚠️ **Critical detail:** `operand2` is popped *first* because it was pushed last. For subtraction and division, order matters: `operand1 - operand2`, NOT the reverse.

---

## 📐 Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Push | O(1) | O(1) |
| Pop | O(1) | O(1) |
| Peek | O(1) | O(1) |
| Postfix Eval | O(n) | O(n) |

The space for the stack itself is O(n) in the worst case (e.g., all numbers before the first operator).

---

## ⚠️ Common Pitfalls

1. **Off-by-one on TOS** — checking `TOS == MAX` instead of `TOS == MAX - 1` for overflow.
2. **Wrong operand order** — for non-commutative operators in postfix, always pop the second operand first.
3. **No input validation** — the project's postfix evaluator exits on invalid expressions. Real systems should handle errors gracefully.
4. **Fixed-size limitation** — array-based stacks have a hard upper limit. Use a linked-list stack (see Topic 14) when size is unbounded.

---

## 🔄 Infix → Postfix Conversion (Shunting Yard)

This project evaluates postfix; conversion from infix is the companion problem. The **Shunting Yard algorithm** (Dijkstra, 1961) uses a stack of operators:

1. If operand → output it directly.
2. If `(` → push it.
3. If `)` → pop and output until `(`.
4. If operator → pop operators of higher/equal precedence first, then push current.
5. At end → pop all remaining operators to output.

---

## 💡 Practice Problems

1. Evaluate the postfix expression: `5 1 2 + 4 * + 3 -`
2. Convert infix `(A + B) * (C - D)` to postfix.
3. Check if a string of brackets `{[()]}` is balanced using a stack.
4. Implement a stack that also supports `getMin()` in O(1) time.
5. Reverse a string using a stack.

---

## 📚 Further Reading

- Cormen et al., *Introduction to Algorithms* (CLRS) — Chapter 10
- Knuth, *The Art of Computer Programming*, Vol. 1
- [Shunting Yard on Wikipedia](https://en.wikipedia.org/wiki/Shunting_yard_algorithm)
