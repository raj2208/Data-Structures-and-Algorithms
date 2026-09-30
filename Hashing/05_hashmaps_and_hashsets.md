# HashMaps and HashSets in C++

## The Four Containers to Know

| Container | Ordered? | Internal Structure | Lookup | Use When |
|-----------|----------|--------------------|--------|----------|
| `std::unordered_map<K,V>` | No | Hash table (chaining) | O(1) avg | Fast key→value lookup |
| `std::unordered_set<K>` | No | Hash table (chaining) | O(1) avg | Fast membership test |
| `std::map<K,V>` | Yes (by key) | Red-black tree | O(log n) | Need sorted keys |
| `std::set<K>` | Yes | Red-black tree | O(log n) | Need sorted elements |

All four are in `<unordered_map>`, `<unordered_set>`, `<map>`, `<set>` respectively.

---

## `std::unordered_map<K, V>`

### Core Operations

```cpp
#include <unordered_map>
#include <string>

std::unordered_map<std::string, int> freq;

// Insert
freq["apple"] = 3;

// Update (operator[] creates the key with default value 0 if absent)
freq["apple"]++;          // safe: creates "apple"=0 then increments to 1
freq["apple"] += 1;       // same

// Safe read (does NOT create the key if absent)
auto it = freq.find("banana");
if (it != freq.end()) {
    int val = it->second;
}

// operator[] for read will INSERT a default value — be careful
int x = freq["missing"];  // now "missing" = 0 exists in the map!

// Check existence without inserting
if (freq.count("apple"))          // count() returns 0 or 1
if (freq.contains("apple"))       // C++20, cleaner

// Delete
freq.erase("apple");

// Iterate (order is arbitrary)
for (auto& [key, val] : freq)     // C++17 structured binding
    std::cout << key << ": " << val << "\n";

// Size
freq.size();
freq.empty();
```

### Frequency Counting Idiom

```cpp
std::unordered_map<char, int> freq;

std::string s = "hello world";
for (char c : s)
    freq[c]++;   // operator[] default-initializes to 0 — perfect for counting

// Find max frequency
int max_freq = 0;
for (auto& [c, cnt] : freq)
    max_freq = std::max(max_freq, cnt);
```

### `find` vs `operator[]`

This is a critical distinction in C++:

```cpp
std::unordered_map<int, int> memo;

// BAD for checking existence: inserts 0 as a side effect
if (memo[key] != 0) { ... }   // now memo[key] exists with value 0

// GOOD: find() never modifies the map
if (memo.find(key) != memo.end()) {
    return memo[key];          // safe to use [] now
}
```

**Rule**: Use `find()` for lookups. Only use `operator[]` when you intend to insert or when you know the key is already present.

---

## `std::unordered_set<K>`

```cpp
#include <unordered_set>

std::unordered_set<int> seen;

// Insert
seen.insert(42);
seen.emplace(42);  // slightly more efficient for complex types

// Check (O(1))
if (seen.count(42))       // 0 or 1
if (seen.contains(42))    // C++20

// Delete
seen.erase(42);           // no-op if absent

// Set operations (no built-in operators, do manually)
std::unordered_set<int> a = {1, 2, 3};
std::unordered_set<int> b = {2, 3, 4};

// Intersection
std::unordered_set<int> inter;
for (int x : a)
    if (b.count(x)) inter.insert(x);

// Union
std::unordered_set<int> uni = a;
for (int x : b) uni.insert(x);
```

---

## `std::map<K, V>` — Sorted HashMap

Internally a **red-black tree**. Keys are always in sorted order (ascending by default).

```cpp
#include <map>

std::map<std::string, int> sorted_freq;
sorted_freq["banana"] = 2;
sorted_freq["apple"]  = 3;
sorted_freq["cherry"] = 1;

// Iterates in sorted key order: apple, banana, cherry
for (auto& [key, val] : sorted_freq)
    std::cout << key << ": " << val << "\n";

// Range queries — only possible with std::map, not unordered_map
auto lo = sorted_freq.lower_bound("b");  // first key >= "b"
auto hi = sorted_freq.upper_bound("c");  // first key > "c"
for (auto it = lo; it != hi; ++it)       // keys in ["b", "c"]
    std::cout << it->first << "\n";

// Floor/ceiling
auto it = sorted_freq.lower_bound("ban"); // first key >= "ban" = "banana"
if (it != sorted_freq.begin()) {
    --it;   // largest key < "ban" = "apple"
}
```

**When to use `map` instead of `unordered_map`**:
- You need keys in sorted order
- You need range queries (`lower_bound`, `upper_bound`)
- You need `prev(it)` / `next(it)` (predecessor/successor in O(log n))

---

## `std::set<K>` — Sorted HashSet

Same as `map` but stores keys only.

```cpp
#include <set>

std::set<int> s = {5, 2, 8, 1, 9};
// Stored as: {1, 2, 5, 8, 9}

s.insert(4);
s.erase(2);
s.count(5);      // 0 or 1

// Predecessor / successor (very useful in interview problems)
auto it = s.lower_bound(6);   // first element >= 6 → points to 8
--it;                          // largest element < 6 → points to 5

// Closest element to x
int x = 6;
auto it = s.lower_bound(x);
int closest;
if (it == s.begin())       closest = *it;
else if (it == s.end())    closest = *std::prev(it);
else {
    int hi = *it;
    int lo = *std::prev(it);
    closest = (hi - x < x - lo) ? hi : lo;
}
```

---

## Custom Hash for Custom Keys

By default, `std::unordered_map` only works with types that have a `std::hash` specialization: `int`, `string`, `pair` is NOT included by default in C++.

### Hashing a `pair<int,int>` (common in grid problems)

```cpp
struct PairHash {
    size_t operator()(const std::pair<int,int>& p) const {
        // Combine the two hashes
        size_t h1 = std::hash<int>{}(p.first);
        size_t h2 = std::hash<int>{}(p.second);
        return h1 ^ (h2 << 32) ^ (h2 >> 32);  // simple combine
    }
};

std::unordered_map<std::pair<int,int>, int, PairHash> visited;
visited[{0, 0}] = 1;
visited[{1, 2}] = 3;
```

### Hashing a `vector<int>`

```cpp
struct VectorHash {
    size_t operator()(const std::vector<int>& v) const {
        size_t seed = v.size();
        for (int x : v)
            seed ^= std::hash<int>{}(x) + 0x9e3779b9 + (seed << 6) + (seed >> 2);
        return seed;
    }
};

std::unordered_map<std::vector<int>, std::string, VectorHash> map;
```

### Using a String Key for Grids

A simpler alternative: encode the key as a string.

```cpp
// For a grid coordinate (row, col):
auto key = std::to_string(row) + "," + std::to_string(col);
std::unordered_set<std::string> visited;
visited.insert(key);
```

This avoids writing a custom hash at the cost of string allocation overhead.

---

## What Can Be a Key?

A type `K` can be a key in `unordered_map<K,V>` if:
1. `std::hash<K>` is defined (or you provide a custom hash)
2. `operator==` is defined for `K`

| Type | Works as unordered key? |
|------|------------------------|
| `int`, `long`, `size_t` | Yes |
| `std::string` | Yes |
| `std::pair<int,int>` | **No** — no default hash |
| `std::vector<int>` | **No** — no default hash |
| `std::array<int,N>` | **No** — no default hash |
| Custom struct | Only if you specialize `std::hash` |

---

## LRU Cache in C++ (Important Interview Problem)

Combine `unordered_map` + `list` for O(1) all operations:

```cpp
#include <unordered_map>
#include <list>

class LRUCache {
    int cap;
    std::list<std::pair<int,int>> cache;      // {key, val}, front = most recent
    std::unordered_map<int, std::list<std::pair<int,int>>::iterator> pos;

public:
    LRUCache(int capacity) : cap(capacity) {}

    int get(int key) {
        auto it = pos.find(key);
        if (it == pos.end()) return -1;
        cache.splice(cache.begin(), cache, it->second);  // move to front
        return it->second->second;
    }

    void put(int key, int value) {
        if (pos.count(key)) {
            pos[key]->second = value;
            cache.splice(cache.begin(), cache, pos[key]);
        } else {
            if ((int)cache.size() == cap) {
                pos.erase(cache.back().first);  // evict LRU
                cache.pop_back();
            }
            cache.emplace_front(key, value);
            pos[key] = cache.begin();
        }
    }
};
```

`list::splice` is O(1) — it relinks pointers without copying. The map stores iterators into the list, so we can jump to any node in O(1) and move it to the front in O(1).

---

## Choosing the Right Container

| Situation | Use |
|-----------|-----|
| Fast lookup/insert/delete, order doesn't matter | `unordered_map` / `unordered_set` |
| Need sorted keys or range queries | `map` / `set` |
| Need predecessor/successor efficiently | `map` / `set` (O(log n)) |
| Counting frequencies | `unordered_map<T, int>` with `operator[]` |
| Visited tracking in BFS/DFS | `unordered_set` |
| Memoization | `unordered_map` |
| Grouping items by property | `unordered_map<key, vector<T>>` |

---

## What's Next

- [06_rolling_hash.md](06_rolling_hash.md) — Rabin-Karp and the rolling hash technique for strings
- [07_common_interview_patterns.md](07_common_interview_patterns.md) — Comprehensive catalog of hashing patterns in interview problems
