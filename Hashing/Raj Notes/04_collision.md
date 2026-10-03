# Collision

A collision happens when two different keys hash to the **same index** in the bucket array.

```
hashCode("cat") → 312 → index 2
hashCode("act") → 312 → index 2   ← collision!
```

Both keys want to live at index 2. You can't just overwrite one with the other — both key-value pairs need to be stored.

## Why collisions are unavoidable

The bucket array has a fixed size (say, 100 slots). The number of possible keys is effectively infinite (any string, any integer). You're squeezing an infinite set into a finite range — by the **pigeonhole principle**, some keys must share a bucket.

A good hash function minimizes collisions by distributing keys as evenly as possible, but it can never eliminate them entirely.

## Two main ways to handle collisions

### 1. Chaining (Separate Chaining)

Each bucket holds a **linked list** (or dynamic array) instead of a single value. When two keys collide, both get added to the list at that bucket.

```
index 2  →  [("cat", val1) → ("act", val2)]
```

Lookup: go to the bucket, then scan the list to find the right key.

### 2. Open Addressing

All entries are stored inside the bucket array itself (no linked lists). When a collision happens, you probe for the next available slot.

- **Linear probing**: try index+1, index+2, index+3...
- **Quadratic probing**: try index+1², index+2², index+3²...
- **Double hashing**: use a second hash function to determine the step size

```
"cat" → index 2 (taken) → try 3 (empty) → store here
```

Lookup: start at the hash index, probe in the same pattern until you find the key or an empty slot.

## Which is better?

| | Chaining | Open Addressing |
|---|---|---|
| Extra memory | Linked list nodes | None (uses array space) |
| Cache performance | Worse (pointer jumps) | Better (sequential memory) |
| Load factor sensitivity | Tolerates high load | Degrades fast above ~0.7 |

Most standard library hash maps (like C++ `unordered_map`) use some form of chaining or a hybrid.
