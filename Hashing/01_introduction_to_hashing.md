# Introduction to Hashing

## What Problem Does Hashing Solve?

Imagine you have a phone book with a million names. To find someone's number, you'd scan from the top — that's O(n). If it's sorted, binary search gets you to O(log n). But what if you could jump *directly* to any entry in O(1)?

That's what hashing does. It converts a **key** (like a name, string, or number) into an **index** in an array using a **hash function**, letting you store and retrieve values in constant time on average.

---

## Core Idea

```
key  -->  hash function  -->  index  -->  value stored at that index
```

Example:
```
"apple"  -->  hash("apple")  -->  42  -->  array[42] = "fruit"
```

The underlying storage is just an **array** (called a hash table or bucket array). The magic is entirely in the hash function — how cleverly it maps keys to indices.

---

## Formal Definition

A **hash function** `h` maps a universe of keys `U` to a set of **slots** (indices) `{0, 1, ..., m-1}` where `m` is the table size:

```
h : U --> {0, 1, ..., m-1}
```

The structure that uses this function to store key-value pairs is called a **hash table**.

---

## Why Not Just Use an Array Directly?

You could use the key itself as an index. For example, if your keys are integers from 0 to 100, just create an array of size 101. Done.

But real-world keys are:
- Strings ("username", "hello@gmail.com")
- Large integers (phone numbers: 9876543210)
- Objects (a User struct)

You can't index an array with a string. And for a phone number, you'd need an array of size 10 billion — that's 80 GB for 64-bit entries. A hash function lets you **compress** that huge key space into a small, manageable array size.

---

## Properties of a Good Hash Function

A hash function must be:

### 1. Deterministic
The same key must always produce the same hash value. If `h("apple") = 42` today, it must be 42 tomorrow too.

### 2. Uniform Distribution
Hash values should be spread evenly across all slots. If 90% of keys map to the same 10 slots, your table degenerates to a linked list. Uniform distribution minimizes **collisions** (two keys mapping to the same slot).

### 3. Fast to Compute
The hash function runs on every insert and lookup. O(1) is the goal. Expensive hash functions defeat the purpose.

### 4. Avalanche Effect (for good functions)
A small change in the key should cause a large, unpredictable change in the hash. This matters most in cryptographic hashing — not required for hash tables, but desirable.

---

## What Is a Collision?

A **collision** occurs when two different keys hash to the same index:

```
h("apple") = 42
h("mango") = 42   <-- collision!
```

Collisions are **inevitable**. By the **Pigeonhole Principle**, if you have more possible keys than slots (which is almost always true), some keys must share a slot.

The question is not how to avoid collisions — it's how to **handle** them gracefully. (See `03_collision_resolution.md`)

---

## Time Complexity

| Operation | Average Case | Worst Case |
|-----------|-------------|------------|
| Insert    | O(1)        | O(n)       |
| Search    | O(1)        | O(n)       |
| Delete    | O(1)        | O(n)       |

**Average O(1)** assumes a good hash function with uniform distribution and a reasonable load factor. The **worst case O(n)** happens when all keys collide into the same slot — this is rare with a good hash function but can happen with adversarial input (a real concern in production systems).

---

## The Mental Model

Think of a hash table as a **filing cabinet** with labeled drawers (indices 0 to m-1). The hash function tells you which drawer to put something in. Most of the time you open the right drawer directly. Occasionally two things land in the same drawer (collision), and you need a system to handle that.

---

## Real-World Uses of Hashing

- **Database indexing**: Find rows without scanning the full table
- **Caching**: Key-value stores like Redis, Memcached
- **Compilers**: Symbol tables map variable names to memory addresses
- **Cryptography**: SHA-256, MD5 (different goals — irreversibility matters here)
- **Data deduplication**: Detect duplicate files by comparing hashes
- **Set membership**: Bloom filters for approximate membership queries
- **Distributed systems**: Consistent hashing for load balancing (see `06_consistent_hashing.md`)

---

## Key Vocabulary

| Term | Meaning |
|------|---------|
| Hash function | Maps a key to an integer index |
| Hash table | Array that stores values at hashed indices |
| Bucket | A slot in the hash table (may hold multiple items if collision occurs) |
| Collision | Two keys producing the same hash index |
| Load factor | Ratio of stored items to table size: `n/m` |
| Rehashing | Rebuilding the table with a larger size when load factor is too high |

---

## What's Next

- [02_hash_functions.md](02_hash_functions.md) — How hash functions actually work (modular, polynomial, universal)
- [03_collision_resolution.md](03_collision_resolution.md) — Chaining vs open addressing
- [04_hash_table_internals.md](04_hash_table_internals.md) — Load factor, resizing, amortized analysis
