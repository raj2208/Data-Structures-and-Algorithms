# Separate Chaining

Also called: **open hashing** or **closed addressing**.

All three names mean the same thing. Don't let the naming confuse you.

- **Separate chaining** → describes what happens: colliding keys get their own chain
- **Open hashing** → the data can "spill out" of the bucket array (into linked lists outside it)
- **Closed addressing** → once a key is hashed to an index, it stays at that index — it never moves to a different bucket

---

## How it works

Each bucket in the array holds a **linked list**. When a key hashes to an index, it gets added to the list at that index. If another key collides there, it just gets appended to the same list.

```
Bucket array (size 5):

index 0  →  null
index 1  →  [("cat", 1)] → [("act", 2)] → null
index 2  →  [("dog", 3)] → null
index 3  →  null
index 4  →  [("rat", 4)] → null
```

`"cat"` and `"act"` both hashed to index 1, so they share the same chain.

---

## Lookup

1. Hash the key to get the index
2. Go to that bucket
3. Walk the linked list until you find the matching key

---

## But doesn't the linked list make it slow?

You might think: walking a linked list is O(n), so how is a hash map O(1)?

The answer is that a good hash function distributes keys **uniformly** across all buckets. If you have 100 buckets and 100 keys, on average each bucket has exactly 1 key in its chain — a list of length 1. Walking a list of length 1 is just one comparison, which is O(1).

Even in practice, as long as the hash function is decent and the load factor is kept reasonable (not too many keys crammed into too few buckets), no individual chain ever grows long enough to matter. The list length stays roughly constant regardless of how many total keys are in the map.

So in the big picture — O(1) average for insert, lookup, and delete. The linked list is there to handle the rare collision, not to store hundreds of items.

## Simple. Flexible. Common.

Separate chaining is easy to implement and handles collisions naturally. Most standard library hash maps use this under the hood.
