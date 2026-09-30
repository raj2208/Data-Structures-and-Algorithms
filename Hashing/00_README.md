# Hashing — Study Guide for Google Interview

## Reading Order

Work through these in order. Each file links to the next.

| # | File | What You'll Learn |
|---|------|-------------------|
| 1 | [01_introduction_to_hashing.md](01_introduction_to_hashing.md) | What hashing is, why it's O(1), vocabulary |
| 2 | [02_hash_functions.md](02_hash_functions.md) | How hash functions actually work (integers, strings, universal) |
| 3 | [03_collision_resolution.md](03_collision_resolution.md) | Chaining vs open addressing (linear, quadratic, double hashing) |
| 4 | [04_hash_table_internals.md](04_hash_table_internals.md) | Load factor, resizing, amortized O(1) proof |
| 5 | [05_hashmaps_and_hashsets.md](05_hashmaps_and_hashsets.md) | Python dict/Counter/defaultdict, Java HashMap/TreeMap in depth |
| 6 | [06_rolling_hash.md](06_rolling_hash.md) | Rabin-Karp and the rolling hash technique for string problems |
| 7 | [07_common_interview_patterns.md](07_common_interview_patterns.md) | 10 patterns that appear repeatedly in Google problems |
| 8 | [08_consistent_hashing.md](08_consistent_hashing.md) | Distributed hashing for system design rounds |

## Core Things to Memorize

- **Hash table time complexity**: O(1) average for insert/search/delete; O(n) worst case
- **Load factor** α = n/m: the ratio that controls performance
- **Separate chaining**: expected O(1 + α) per operation
- **Open addressing**: blows up as α → 1; keep α < 0.7
- **Amortized O(1) insert**: doubling table size makes total resize cost O(n) for n inserts
- **Two-sum template**: store seen values in a map, look up complement on each step
- **Prefix sum + hash map**: for "how many subarrays satisfy X" problems
- **Rolling hash**: remove leftmost char's contribution, multiply, add new char — all mod large prime
- **Consistent hashing**: keys and servers on a ring; add/remove moves only K/n keys
