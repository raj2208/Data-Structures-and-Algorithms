# Consistent Hashing

## Why This Matters for Google Interviews

Consistent hashing is a **system design concept**, not a coding problem. But Google explicitly asks about it in design rounds — it underpins how large-scale distributed systems like Google's Bigtable, Amazon's DynamoDB, Apache Cassandra, and Content Delivery Networks (CDNs) work.

Expect questions like:
- "How would you design a distributed cache?"
- "How would you shard data across servers?"
- "What happens when a server goes down in your design?"

---

## The Problem: Naive Modular Sharding

Suppose you have `n` servers and want to distribute requests (or data) evenly. Simple approach:

```
server = hash(key) % n
```

This works fine while `n` is fixed. But in production, servers fail and new servers are added constantly.

**What happens when `n` changes?**

Say you have 3 servers and go to 4:
- Old: `hash(key) % 3`
- New: `hash(key) % 4`

Almost every key remaps to a different server. If these are cache servers, you just invalidated **nearly your entire cache**. If these are database shards, you need to migrate almost all your data.

With `n` servers and going to `n+1`, roughly `n/(n+1)` of all keys move — nearly all of them.

**This is the rehashing problem in distributed systems.**

---

## Consistent Hashing: The Solution

### The Ring

Map **both keys and servers** to positions on a circle (ring) using the same hash function. The ring represents the hash space `[0, 2^32 - 1]` (or any large integer range) wrapped around.

```
        0
   330     30
 300         60
 
 270    Ring   90
 
 240         120
   210     150
        180
```

**Placing servers**: Hash the server's identifier (IP + port, or just name) to a position on the ring.
```
hash("server-1") = 90   → server-1 sits at position 90
hash("server-2") = 210  → server-2 sits at position 210
hash("server-3") = 330  → server-3 sits at position 330
```

**Assigning keys to servers**: Hash the key to a position. Walk **clockwise** until you hit a server. That server owns the key.

```
hash("user:42") = 120   → walk clockwise from 120 → hit server-2 at 210
hash("user:99") = 50    → walk clockwise from 50 → hit server-1 at 90
hash("data:x")  = 250   → walk clockwise from 250 → hit server-3 at 330
```

---

## What Happens When a Server Is Added

Add `server-4` at position 150.

Before: keys from 210 (exclusive) → 330 (inclusive) were at server-3.
After: keys from 210 (exclusive) → 150 (inclusive) moving clockwise → land at server-4.

Only the keys **between server-2 (210) and server-4 (150)** move. All other keys stay put.

**How many keys move?** On average, only `K/n` keys (where `K` = total keys, `n` = servers). For a large cache, this is the minimum possible disruption.

---

## What Happens When a Server Fails

Remove `server-2` at position 210.

Keys that were at server-2 now walk clockwise past 210 and hit the next server (server-3 at 330).

Again, only `K/n` keys are affected — only those that were assigned to server-2.

---

## Virtual Nodes (vnodes)

### The Problem with Simple Consistent Hashing

With only 3 physical servers, the ring may be unevenly partitioned:
```
server-1: 330° → 90°   (120° of the ring = 33%)
server-2: 90°  → 210°  (120° of the ring = 33%)
server-3: 210° → 330°  (120° of the ring = 33%)
```

This is accidentally balanced, but with real hash functions and real server IDs, the distribution is rarely this uniform. One server might own 60% of the ring, another 5%.

Also, if server-3 fails, all its load goes to server-1, doubling server-1's load. That's a **cascade failure**.

### The Solution: Virtual Nodes

Assign each **physical server** multiple positions on the ring (virtual nodes). Each physical server has, say, 150 virtual nodes spread around the ring.

**Benefits**:
1. **Load balancing**: 150 random positions spread more evenly than 1
2. **Graceful failure**: When a server fails, its 150 virtual nodes disappear from the ring. Its keys distribute among all remaining servers (each gets roughly `150/total_vnodes` fraction of the failed server's load)
3. **Heterogeneous capacity**: Bigger servers get more virtual nodes

**Typical vnode counts**: 100–200 per physical node in production systems.

---

## C++ Implementation

```cpp
#include <map>
#include <string>
#include <functional>
#include <sstream>

class ConsistentHash {
    int vnodes;
    std::map<uint32_t, std::string> ring;  // position → server_id (sorted!)

    uint32_t hash_key(const std::string& key) const {
        // FNV-1a 32-bit
        uint32_t h = 2166136261u;
        for (unsigned char c : key) {
            h ^= c;
            h *= 16777619u;
        }
        return h;
    }

public:
    explicit ConsistentHash(int vnodes = 150) : vnodes(vnodes) {}

    void add_server(const std::string& server_id) {
        for (int i = 0; i < vnodes; ++i) {
            std::string vnode_key = server_id + "#vnode" + std::to_string(i);
            ring[hash_key(vnode_key)] = server_id;
        }
    }

    void remove_server(const std::string& server_id) {
        for (int i = 0; i < vnodes; ++i) {
            std::string vnode_key = server_id + "#vnode" + std::to_string(i);
            ring.erase(hash_key(vnode_key));
        }
    }

    std::string get_server(const std::string& key) const {
        if (ring.empty()) return "";
        uint32_t pos = hash_key(key);
        // Binary search for first position >= pos (clockwise walk)
        auto it = ring.lower_bound(pos);
        if (it == ring.end()) it = ring.begin();  // wrap around
        return it->second;
    }
};

// Usage:
// ConsistentHash ch;
// ch.add_server("server-1");
// ch.add_server("server-2");
// ch.add_server("server-3");
// ch.get_server("user:12345");  // → one of the servers
// ch.remove_server("server-2"); // only ~1/3 of keys remap
```

**Why `std::map` instead of `unordered_map`?** `std::map` is a sorted red-black tree. `lower_bound()` on a sorted map gives us the "next clockwise server" in O(log(n × vnodes)). `unordered_map` doesn't support `lower_bound`.

---

## Consistent Hashing in Real Systems

| System | Use Case |
|--------|----------|
| Amazon DynamoDB | Partition key → node mapping |
| Apache Cassandra | Token ring for data partitioning |
| Memcached (libketama) | Distribute cache across servers |
| Netflix Zuul | Route requests to backend instances |
| CDNs (Akamai, Cloudflare) | Map URLs to edge servers |
| Discord | Shard users across message servers |

---

## Consistent Hashing vs Random Sharding vs Range Sharding

| Approach | Distribution | Rebalancing on Change | Use Case |
|----------|-------------|----------------------|----------|
| Modular (`key % n`) | Even | Catastrophic (rehash all) | Fixed-size cluster |
| Consistent hashing | Slightly uneven (fixed by vnodes) | Minimal (K/n keys move) | Dynamic clusters |
| Range sharding | Can be uneven (hot keys) | Moderate (split ranges) | Sorted/range queries |
| Rendezvous (HRW) | Even | Minimal | Simpler alternative to consistent hashing |

### Rendezvous Hashing (Highest Random Weight)

A simpler alternative to ring-based consistent hashing:

```cpp
std::string get_server_hrw(const std::string& key,
                            const std::vector<std::string>& servers) {
    uint32_t best_hash = 0;
    std::string best_server;
    for (const auto& s : servers) {
        uint32_t h = fnv1a(key + ":" + s);  // combined hash
        if (h > best_hash) { best_hash = h; best_server = s; }
    }
    return best_server;
}
```

- Same minimal disruption property as consistent hashing
- Simpler implementation (no ring data structure needed)
- Slower: O(n) per lookup vs O(log n) for consistent hashing
- Good for small server counts

---

## Key Points to Mention in a Google Interview

1. **Naive `key % n` fails** when servers are added/removed — almost all keys remap
2. **Consistent hashing** maps keys and servers to a ring; key → nearest clockwise server
3. When a server is added/removed, only `~K/n` keys remap (minimum disruption)
4. **Virtual nodes** fix load imbalance and enable graceful failure spreading
5. In C++, use `std::map` (sorted by position) + `lower_bound` for O(log n) lookups
6. In an interview, mention: ring data structure, binary search for lookup, virtual nodes, what happens on add/remove

---

## Summary of All Hashing Files

| File | Topic |
|------|-------|
| [01_introduction_to_hashing.md](01_introduction_to_hashing.md) | What hashing is, why it exists, core vocabulary |
| [02_hash_functions.md](02_hash_functions.md) | Division, multiplication, polynomial rolling, universal hashing, C++ `std::hash` |
| [03_collision_resolution.md](03_collision_resolution.md) | Chaining, linear/quadratic probing, double hashing, tombstones |
| [04_hash_table_internals.md](04_hash_table_internals.md) | Load factor, dynamic resizing, amortized O(1) proof, `unordered_map` internals |
| [05_hashmaps_and_hashsets.md](05_hashmaps_and_hashsets.md) | `unordered_map`, `unordered_set`, `map`, `set`, custom hash, LRU cache |
| [06_rolling_hash.md](06_rolling_hash.md) | Rabin-Karp, sliding window hashing, prefix hash arrays |
| [07_common_interview_patterns.md](07_common_interview_patterns.md) | 10 patterns: frequency, two-sum, prefix sum, sliding window, etc. |
| [08_consistent_hashing.md](08_consistent_hashing.md) | Distributed systems: ring, virtual nodes, real-world systems |
