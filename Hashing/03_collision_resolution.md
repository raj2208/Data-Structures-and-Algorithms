# Collision Resolution

## Why Collisions Are Inevitable

By the **Pigeonhole Principle**: if you have more keys than slots, at least two keys must share a slot. In practice, even with far fewer keys than slots, the **Birthday Paradox** tells us collisions happen sooner than you'd expect:

> In a group of just 23 people, there's a 50% chance two share a birthday (365 possible days).

With `m` slots and `n` keys, expect the first collision around when `n ≈ sqrt(m)`. For a 1000-slot table, expect a collision after ~32 insertions. Collisions are facts of life.

Two major strategies to handle them: **Separate Chaining** and **Open Addressing**.

---

## Strategy 1: Separate Chaining (Closed Addressing)

### Core Idea
Each slot in the table holds a **linked list** (or dynamic array) of all entries that hash to that slot.

```
Table:
Index 0: --> [("orange", 7)] --> null
Index 1: --> null
Index 2: --> [("apple", 3)] --> [("mango", 9)] --> null   <-- collision here
Index 3: --> [("grape", 2)] --> null
```

### Operations

**Insert**:
1. Compute `h(key) = i`
2. Prepend `(key, value)` to the list at `table[i]`
3. Time: O(1)

**Search**:
1. Compute `h(key) = i`
2. Walk the list at `table[i]`, comparing keys
3. Time: O(length of chain at i)

**Delete**:
1. Compute `h(key) = i`
2. Walk the list, remove the matching node
3. Time: O(length of chain at i)

### Analysis with Load Factor

Define **load factor** `α = n / m` where `n` = number of entries, `m` = table size.

Assuming **Simple Uniform Hashing** (each key equally likely to hash to any slot):
- Expected length of any chain = `α`
- Search time = O(1 + α)

If `α = O(1)` (keep load factor bounded by a constant), then search is O(1).
If `α = n` (worst case, all keys in one chain), search is O(n).

### C++ Implementation (Separate Chaining)

```cpp
#include <vector>
#include <list>
#include <string>

template<typename K, typename V>
class ChainedHashTable {
    int m;
    std::vector<std::list<std::pair<K, V>>> table;

    int hash(const K& key) const {
        return std::hash<K>{}(key) % m;
    }

public:
    explicit ChainedHashTable(int m = 16) : m(m), table(m) {}

    void insert(const K& key, const V& value) {
        int i = hash(key);
        for (auto& [k, v] : table[i]) {
            if (k == key) { v = value; return; }  // update existing
        }
        table[i].emplace_front(key, value);
    }

    V* find(const K& key) {
        int i = hash(key);
        for (auto& [k, v] : table[i])
            if (k == key) return &v;
        return nullptr;
    }

    void erase(const K& key) {
        int i = hash(key);
        table[i].remove_if([&](const auto& p) { return p.first == key; });
    }
};
```

### What C++'s `std::unordered_map` Uses Under the Hood

`std::unordered_map` uses **separate chaining**. Each bucket is a linked list of nodes. When the load factor exceeds `max_load_factor()` (default 1.0), it rehashes.

```cpp
#include <unordered_map>

std::unordered_map<std::string, int> m;
m.max_load_factor();    // 1.0 by default
m.bucket_count();       // current number of buckets
m.load_factor();        // current n/m
m.reserve(1000);        // preallocate for ~1000 elements (avoids rehashes)
```

---

## Strategy 2: Open Addressing

### Core Idea
Everything is stored **inside the table itself** — no external linked lists. When a collision occurs, you **probe** for the next available slot using some deterministic sequence.

All `n` entries occupy slots in the single array. This means:
- Load factor `α = n/m` must stay below 1 (table can be full)
- Memory is more cache-friendly (array of records, no pointer chasing)

### 2a. Linear Probing

```
h(key, i) = (h'(key) + i) mod m
```

On the `i`-th collision, try the next slot linearly (i = 0, 1, 2, ...).

**Example**: Insert keys that hash to slot 4, then 5, then 4 again:
```
h(A) = 4  --> place at 4
h(B) = 5  --> place at 5
h(C) = 4  --> 4 is taken, try 5 → also taken, try 6 → place at 6
h(D) = 6  --> 6 is taken, try 7 → place at 7
```

**Primary Clustering Problem**: Once a cluster forms, future insertions into nearby slots extend the cluster, making it worse. Long contiguous runs of occupied slots slow down operations.

```cpp
template<typename K, typename V>
void linear_probe_insert(std::vector<std::pair<K,V>>& table,
                         const K& key, const V& value, int m) {
    int i = std::hash<K>{}(key) % m;
    while (/* table[i] is occupied */ table[i].first != K{}) {
        if (table[i].first == key) { table[i].second = value; return; }
        i = (i + 1) % m;
    }
    table[i] = {key, value};
}
```

**Deletion is tricky**: If you delete a slot and leave it empty, a search that passed through that slot during insertion won't find the target (the empty slot signals "not found" prematurely). Solution: use a **tombstone** marker — a deleted slot is marked as "was occupied" so probing continues past it.

### 2b. Quadratic Probing

```
h(key, i) = (h'(key) + c1*i + c2*i²) mod m
```

Common choice: `c1 = 0, c2 = 1`:
```
probe sequence: h'(key), h'(key)+1, h'(key)+4, h'(key)+9, h'(key)+16, ...
```

**Reduces primary clustering**: Keys with the same initial hash follow the same probe sequence (called **secondary clustering**), but keys with *different* initial hashes don't cluster together.

**Warning**: If `m` is not prime, the probe sequence may not cover all slots. With `m` prime and `α < 0.5`, quadratic probing is guaranteed to find an empty slot.

```cpp
int quadratic_probe_slot(const std::vector<bool>& occupied,
                         int start, int m) {
    for (int i = 0; ; ++i) {
        int slot = (start + i * i) % m;
        if (!occupied[slot]) return slot;
    }
}
```

### 2c. Double Hashing

```
h(key, i) = (h1(key) + i * h2(key)) mod m
```

Use two independent hash functions. The step size `h2(key)` depends on the key itself, so even keys with the same `h1` value follow *different* probe sequences.

**Eliminates secondary clustering**.

Common choice:
```
h1(k) = k mod m
h2(k) = 1 + (k mod (m-1))   // must be coprime with m; never 0
```

If `h2(key)` can return 0, you'd probe the same slot forever. The `1 +` prevents this.

```cpp
int double_hash_slot(const std::vector<bool>& occupied,
                     int key, int m) {
    int h1 = key % m;
    int h2 = 1 + (key % (m - 1));
    for (int i = 0; ; ++i) {
        int slot = (h1 + i * h2) % m;
        if (!occupied[slot]) return slot;
    }
}
```

Double hashing approximates **random probing** (the theoretical ideal), achieving near-optimal performance.

---

## Comparison: Chaining vs Open Addressing

| Aspect | Separate Chaining | Open Addressing |
|--------|------------------|-----------------|
| Load factor limit | Can exceed 1.0 | Must stay < 1.0 |
| Memory | Extra pointers per node | Dense array, no extra pointers |
| Cache performance | Poor (pointer chasing) | Good (sequential array) |
| Deletion | Simple (just remove node) | Needs tombstones |
| Worst-case behavior | O(n) per slot | O(n) if overfull |
| Implementation | Simpler | More subtle |
| C++ standard library | `std::unordered_map` | Not in stdlib; used in flat_hash_map (Abseil) |

**Rule of thumb**: 
- Separate chaining is safer and more predictable
- Open addressing is faster in practice due to cache locality (especially linear probing) — but only when load factor is kept low (< 0.7)
- Google's Abseil library (`absl::flat_hash_map`) uses open addressing and is significantly faster than `std::unordered_map` in benchmarks

---

## Comparison: Linear vs Quadratic vs Double Hashing

| Method | Primary Clustering | Secondary Clustering | Full Table Guarantee |
|--------|-------------------|---------------------|---------------------|
| Linear Probing | Yes (bad) | No | Yes (always finds a slot if α < 1) |
| Quadratic Probing | No | Yes (moderate) | Only if m is prime and α < 0.5 |
| Double Hashing | No | No | Yes if h2 and m are coprime |

---

## Deletion in Open Addressing

This is a subtle and commonly tested concept.

**The Problem**:
```
Insert A at slot 3
Insert B at slot 3 → probes → lands at slot 4
Delete A from slot 3 → mark slot 3 as empty
Search for B → compute h(B) = 3 → slot 3 is empty → WRONG: reports B not found!
```

**Solution — Tombstone Markers**:
Mark deleted slots with a special `DELETED` sentinel (tombstone). During search, continue probing past tombstones. During insert, you can *reuse* a tombstone slot.

```cpp
enum class SlotState { EMPTY, OCCUPIED, DELETED };

template<typename K, typename V>
struct Slot {
    K key;
    V value;
    SlotState state = SlotState::EMPTY;
};

template<typename K, typename V>
V* search(std::vector<Slot<K,V>>& table, const K& key, int m) {
    int start = std::hash<K>{}(key) % m;
    int i = start;
    do {
        if (table[i].state == SlotState::EMPTY) return nullptr;
        if (table[i].state == SlotState::OCCUPIED && table[i].key == key)
            return &table[i].value;
        i = (i + 1) % m;
    } while (i != start);
    return nullptr;
}
```

**Tombstone downside**: Over time, a table full of tombstones degrades performance because searches probe through them unnecessarily. Periodic **rehashing** (rebuilding the table fresh) clears tombstones.

---

## What's Next

- [04_hash_table_internals.md](04_hash_table_internals.md) — Load factor, dynamic resizing, and amortized analysis
