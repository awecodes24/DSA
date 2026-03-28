# 08 — Hashing

> **Files in project:** _(not implemented)_  
> **Status:** Full study guide

---

## 🧠 Concept Overview

**Hashing** maps data of arbitrary size to a fixed-size value (a hash) using a **hash function**. The goal is to enable **O(1) average-case** insertion, deletion, and lookup — faster than any comparison-based structure.

Hashing is the backbone of Python's `dict`, Java's `HashMap`, database indexes, caches, and cryptographic systems.

---

## 📖 Key Terminology

| Term | Meaning |
|------|---------|
| Hash Function | Maps a key to an index: `h(key) = index` |
| Hash Table | Array indexed by hash values |
| Bucket | A slot in the hash table |
| Collision | When two keys hash to the same index |
| Load Factor | `α = n / m` — n items, m slots |
| Chaining | Collision resolution: each bucket holds a linked list |
| Open Addressing | Collision resolution: probe for next empty slot |
| Linear Probing | Probe next slot sequentially: `h(k, i) = (h(k) + i) mod m` |
| Quadratic Probing | `h(k, i) = (h(k) + i²) mod m` |
| Double Hashing | `h(k, i) = (h₁(k) + i·h₂(k)) mod m` |
| Rehashing | Rebuild the table with a larger size when load factor is too high |

---

## 🔧 Hash Functions

### Division Method
```python
def h(key, m):
    return key % m     # m should be a prime not close to a power of 2
```

### Multiplication Method
```python
import math
A = 0.6180339887    # (√5 - 1) / 2 — Knuth's suggestion
def h(key, m):
    return int(m * ((key * A) % 1))
```

### String Hashing (Polynomial Rolling Hash)
```python
def h(s, m, p=31):
    result = 0
    p_pow = 1
    for c in s:
        result = (result + (ord(c) - ord('a') + 1) * p_pow) % m
        p_pow = (p_pow * p) % m
    return result
```

**Good hash function properties:**
1. **Deterministic** — same key always produces same hash.
2. **Uniform distribution** — spread keys evenly across buckets.
3. **Fast to compute** — O(1) or O(key length).
4. **Avalanche effect** — small input change → large hash change.

---

## 🔧 Collision Resolution

### 1. Chaining

Each bucket stores a linked list of all keys that hash there.

```
Bucket 0: → [key=10] → [key=30] → NULL
Bucket 1: → [key=21] → NULL
Bucket 2: → NULL
Bucket 3: → [key=3] → [key=33] → NULL
```

```python
class HashTableChaining:
    def __init__(self, m):
        self.m = m
        self.table = [[] for _ in range(m)]

    def insert(self, key, value):
        idx = hash(key) % self.m
        for i, (k, v) in enumerate(self.table[idx]):
            if k == key:
                self.table[idx][i] = (key, value)   # update
                return
        self.table[idx].append((key, value))

    def search(self, key):
        idx = hash(key) % self.m
        for k, v in self.table[idx]:
            if k == key:
                return v
        return None

    def delete(self, key):
        idx = hash(key) % self.m
        self.table[idx] = [(k, v) for k, v in self.table[idx] if k != key]
```

**Average complexity:** O(1 + α) where α = load factor. When α ≤ 0.75, expected O(1).

---

### 2. Open Addressing — Linear Probing

When a collision occurs, scan forward to find the next empty slot.

```python
class HashTableLinearProbing:
    def __init__(self, m):
        self.m = m
        self.table = [None] * m
        self.deleted = [False] * m   # tombstones for deleted slots

    def insert(self, key, value):
        idx = hash(key) % self.m
        i = 0
        while self.table[(idx + i) % self.m] is not None and i < self.m:
            if self.table[(idx + i) % self.m][0] == key:
                self.table[(idx + i) % self.m] = (key, value)
                return
            i += 1
        self.table[(idx + i) % self.m] = (key, value)

    def search(self, key):
        idx = hash(key) % self.m
        i = 0
        while self.table[(idx + i) % self.m] is not None and i < self.m:
            if self.table[(idx + i) % self.m][0] == key:
                return self.table[(idx + i) % self.m][1]
            i += 1
        return None
```

**Primary clustering:** Linear probing creates long runs of occupied slots, degrading performance. Quadratic probing or double hashing mitigate this.

---

### 3. Double Hashing

Use two hash functions to compute the probe step:

```
h(k, i) = (h₁(k) + i × h₂(k)) mod m
h₁(k) = k mod m
h₂(k) = 1 + (k mod (m - 1))    # must be coprime to m
```

Eliminates clustering patterns. Near-random distribution of probes.

---

## 📐 Complexity Analysis

| Operation | Chaining (avg) | Chaining (worst) | Open Addr (avg α<0.5) |
|-----------|---------------|-----------------|----------------------|
| Insert | O(1) | O(n) | O(1) |
| Search | O(1 + α) | O(n) | O(1/(1-α)) |
| Delete | O(1 + α) | O(n) | O(1/(1-α)) |

Worst case (all keys collide) is O(n). With a good hash function and load factor < 0.75, **expected O(1)**.

---

## 🔄 Rehashing

When load factor `α = n/m` exceeds a threshold (typically 0.7–0.75):
1. Allocate a new table of size ~2m (choose next prime).
2. Re-insert all existing elements into the new table.

```python
def rehash(self):
    old_table = self.table
    self.m = next_prime(2 * self.m)
    self.table = [[] for _ in range(self.m)]
    for bucket in old_table:
        for key, value in bucket:
            self.insert(key, value)
```

Rehashing is O(n) but happens infrequently — **amortized O(1) per insertion**.

---

## 🔐 Cryptographic vs. Non-Cryptographic Hash Functions

| Type | Examples | Use Case |
|------|---------|---------|
| Non-cryptographic | MurmurHash, FNV, xxHash | Hash tables — fast, no security |
| Cryptographic | SHA-256, MD5 (broken), SHA-3 | Passwords, certificates — secure |

Cryptographic hashes are too slow for hash tables. Never use `MD5/SHA` for hash table indexing.

---

## ⚠️ Common Pitfalls

1. **Bad hash function** — clustering leads to O(n) performance.
2. **Mutable keys** — if a key changes after insertion, it becomes unreachable.
3. **Forgetting tombstones** — in open addressing, a deleted slot must be marked; otherwise, searches stop prematurely.
4. **Load factor too high** — always rehash before α > 0.75.
5. **Hash DoS attacks** — malicious input can cause many collisions in predictable hash functions. Python 3.3+ uses hash randomization (`PYTHONHASHSEED`).

---

## 💡 Practice Problems

1. Implement a hash map from scratch using chaining.
2. Find the first non-repeating character in a string.
3. Group anagrams together.
4. Two Sum — find two numbers that add to a target.
5. Implement LRU Cache using a hash map + doubly linked list.
6. Find all pairs with a given sum in an array.

---

## 📚 Further Reading

- CLRS Chapter 11 — Hash Tables
- *Python Cookbook* — Chapter 1 (dict internals)
- [How Python dict works](https://www.fluentpython.com) — Chapter 3
