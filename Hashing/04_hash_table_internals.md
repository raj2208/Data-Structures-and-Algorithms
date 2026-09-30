# Hash Table Internals: Load Factor, Resizing, and Amortized Analysis

## The Load Factor

The **load factor** `α` is the single most important parameter controlling hash table performance:

```
α = n / m

where:
  n = number of key-value pairs currently stored
  m = number of slots (current table size)
```

### Why It Matters

For **separate chaining**: Expected search time = O(1 + α)
- α = 0.5 → ~1.5 comparisons per lookup (fast)
- α = 1.0 → ~2 comparisons per lookup (acceptable)
- α = 10.0 → ~11 comparisons per lookup (slow — table is too crowded)

For **open addressing**: Expected number of probes = approximately `1 / (1 - α)`
- α = 0.5 → 2 probes
- α = 0.9 → 10 probes
- α = 0.99 → 100 probes (table nearly full — catastrophically slow)

This is why open addressing must maintain a low load factor. The formula `1/(1-α)` blows up as α → 1.

### Typical Load Factor Thresholds

| Implementation | Strategy | Max Load Factor | Notes |
|----------------|----------|-----------------|-------|
| C++ `std::unordered_map` | Separate chaining | 1.0 (default) | Configurable via `max_load_factor()` |
| Abseil `flat_hash_map` | Open addressing | ~7/8 | Faster than std in practice |
| Go `map` | Hybrid | ~6.5 avg per bucket | Bucket-based |
| Java `HashMap` | Separate chaining | 0.75 (default) | Configurable |

---

## Dynamic Resizing (Rehashing)

A hash table starts with some initial capacity `m`. As you add more elements, `α` grows. When `α` exceeds the threshold, the table must **resize**.

### The Process

1. Allocate a **new array** of size `m' = 2m` (doubling is standard)
2. For each existing key-value pair in the old table:
   - Compute the new hash: `h(key) % m'`
   - Insert into the new table
3. Discard the old table

```cpp
#include <vector>
#include <list>

template<typename K, typename V>
class HashTable {
    int m;
    int n = 0;
    float max_load = 0.75f;
    std::vector<std::list<std::pair<K, V>>> buckets;

    int bucket_idx(const K& key) const {
        return std::hash<K>{}(key) % m;
    }

    void resize() {
        int new_m = m * 2;
        std::vector<std::list<std::pair<K, V>>> new_buckets(new_m);
        for (auto& chain : buckets)
            for (auto& [k, v] : chain)
                new_buckets[std::hash<K>{}(k) % new_m].emplace_back(k, v);
        buckets = std::move(new_buckets);
        m = new_m;
    }

public:
    explicit HashTable(int initial = 16) : m(initial), buckets(initial) {}

    void insert(const K& key, const V& value) {
        if ((float)(n + 1) / m > max_load) resize();
        int i = bucket_idx(key);
        for (auto& [k, v] : buckets[i])
            if (k == key) { v = value; return; }
        buckets[i].emplace_back(key, value);
        ++n;
    }
};
```

### Why Double the Size?

Doubling (not incrementing by a fixed amount) is what makes the **amortized cost O(1)**. Here's why:

---

## Amortized Analysis of Resizing

### The Aggregate Method

Consider inserting `n` elements starting from a table of size 1.

Without resizing, all `n` inserts cost O(1) each — total O(n).
But we also resize at sizes 1, 2, 4, 8, ..., 2^k (whenever table doubles).

When we resize at size `s`, we copy `s` elements. Resizing happens at:
- Size 1 → copy 1 element
- Size 2 → copy 2 elements
- Size 4 → copy 4 elements
- Size 8 → copy 8 elements
- ...
- Size n/2 → copy n/2 elements

Total copy work = 1 + 2 + 4 + 8 + ... + n/2 = **n - 1** (geometric series)

So for `n` insertions:
- `n` actual insertions: cost `n`
- All resizing combined: cost `n - 1`
- Total: `2n - 1` = **O(n)**

Dividing by n insertions: **amortized O(1) per insert**.

### The Accounting Method (Intuition)

Imagine each element carries 3 "coins":
- 1 coin to pay for its own insertion
- 2 coins set aside for the *next* resize (it'll need to be moved twice before another resize)

When we resize, every element funds its own relocation from its stored coins. The savings bank always has enough. Each element ultimately pays constant total cost.

### Why Not Increment by a Fixed Amount?

If we grew by +1 each time:
- Insert 1: resize from 0→1 (copy 0)
- Insert 2: resize from 1→2 (copy 1)
- Insert 3: resize from 2→3 (copy 2)
- ...
- Insert n: resize from n-1→n (copy n-1)

Total copy work = 0 + 1 + 2 + ... + (n-1) = **O(n²)**
Amortized per insert = **O(n)** — terrible.

---

## Shrinking the Table

Most implementations also **shrink** the table when load factor drops too low (typically α < 0.25). This prevents wasted memory after many deletions.

If shrink threshold is α < 0.25 and grow threshold is α > 0.75:

- Grow when α > 0.75 → double size
- Shrink when α < 0.25 → halve size

These thresholds maintain a **factor-of-3 gap** between them, preventing "thrashing" (alternating between resize and shrink on every operation near the boundary).

If the gap were too small — say grow at 0.6, shrink at 0.5 — then inserting then deleting repeatedly near the boundary would trigger O(n) resize on every operation.

---

## Preallocation in C++

If you know roughly how many elements you'll store, **preallocate** to avoid rehashes:

```cpp
#include <unordered_map>

// reserve() tells unordered_map to preallocate enough buckets so that
// inserting n elements won't trigger a rehash (respects max_load_factor)
std::unordered_map<int, int> freq;
freq.reserve(1000);           // preallocate for ~1000 inserts

// Or set max_load_factor first, then reserve
freq.max_load_factor(0.5f);   // denser → fewer collisions, more memory
freq.reserve(1000);

// Check current state
std::cout << freq.bucket_count() << '\n';  // number of buckets
std::cout << freq.load_factor() << '\n';   // current n/m
```

`std::unordered_map` starts with a small bucket count (typically 1 or 8 depending on implementation). For storing a million items without preallocation, you'll trigger ~17 rehashes. Preallocation avoids this.

---

## Memory Layout: `std::unordered_map` Internals

```
buckets array: [ptr] [ptr] [ptr] [ptr] ...
                 |         |
                 v         v
              Node(k1,v1)  Node(k3,v3)
                 |
                 v
              Node(k2,v2) -> nullptr
```

`std::unordered_map` stores:
- A `vector` of bucket pointers (the hash table proper)
- Each bucket is a singly-linked list of `node` objects allocated on the heap

This means:
- Every insert allocates a new node (heap allocation)
- Iteration over all elements requires traversing all buckets and all chains
- Cache-unfriendly: pointer chasing between nodes

**Abseil `flat_hash_map`** (preferred for performance) stores everything in a single flat array — more cache friendly, no per-node allocation.

```cpp
// If you can use Abseil (Google's C++ library used internally):
#include "absl/container/flat_hash_map.h"
absl::flat_hash_map<int, int> map;  // 2–3x faster than std::unordered_map
```

---

## Thread Safety in C++

`std::unordered_map` is **not thread-safe**. Concurrent reads are safe; any write requires external synchronization:

```cpp
#include <shared_mutex>

std::unordered_map<int, int> map;
std::shared_mutex mu;

// Multiple readers at once
{
    std::shared_lock lock(mu);
    auto it = map.find(key);
}

// One writer at a time
{
    std::unique_lock lock(mu);
    map[key] = value;
}
```

For high-concurrency workloads, use **striped locking**: partition the map into `k` shards, each with its own mutex. Threads on different shards don't block each other.

---

## Complete Complexity Summary

| Operation | Average | Worst | Notes |
|-----------|---------|-------|-------|
| Insert | O(1) amortized | O(n) | O(n) if resize; O(n) if all collide |
| Search | O(1) | O(n) | O(n) if all keys in one chain |
| Delete | O(1) | O(n) | Tombstones in open addressing |
| Resize | O(n) | O(n) | Happens O(log n) times for n inserts |
| Space | O(n) | O(n) | Plus overhead (empty slots, pointers) |

---

## What's Next

- [05_hashmaps_and_hashsets.md](05_hashmaps_and_hashsets.md) — C++ unordered_map, unordered_set, map, set — and when to use each
- [06_rolling_hash.md](06_rolling_hash.md) — Rabin-Karp and rolling hash technique
