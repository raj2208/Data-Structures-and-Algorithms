# Open Addressing

Also called: **closed hashing**.

Again, two names for the same thing:
- **Open addressing** → the address of a key is not fixed; it's "open" to be somewhere else in the array
- **Closed hashing** → everything stays inside the bucket array, nothing spills out

---

## How it works

There are no linked lists. All key-value pairs live directly inside the bucket array. When a collision happens, you **probe** — look for the next available slot in the array according to some rule — and store the key there instead.

```
"cat" hashes to index 2, but index 2 is taken.
Probe → try index 3 → empty → store "cat" here.
```

---

## The golden rule

When looking up a key, you follow the **same probing sequence** you used during insertion. You stop when you either find the key or hit an empty slot (which means the key doesn't exist).

---

## Lookup example

```
Looking up "cat":
  hash("cat") → index 2 → not "cat" here, keep probing
  try index 3 → found "cat" ✓
```

---

## Load factor matters a lot here

The **load factor** is how full the array is: `number of entries / array size`.

With separate chaining, a high load factor just means longer chains — manageable. With open addressing, as the array fills up, probing sequences get longer and longer, and performance tanks fast. Typically you resize the array once the load factor exceeds **0.7**.

---

## Probing strategies

How you pick the next slot to try is what separates the strategies.

---

### Linear Probing

Check the next slot, then the next, one step at a time.

```
probe sequence: index, index+1, index+2, index+3, ...
```

**Example** — array size 7, "cat", "act", "tac" all hash to index 3:

```
Insert "cat" → index 3 empty → store at 3
Insert "act" → index 3 taken → try 4 → empty → store at 4
Insert "tac" → index 3 taken → try 4 taken → try 5 → empty → store at 5

index:  0     1     2     3      4      5      6
      [ ]   [ ]   [ ]  [cat]  [act]  [tac]  [ ]
```

The problem: keys pile up next to each other — called **primary clustering**. Once a cluster forms, any key that hashes near it makes the cluster bigger. Probing sequences get longer and performance degrades.

---

### Quadratic Probing

Instead of stepping by 1, the steps grow quadratically — 1², 2², 3²...

```
probe sequence: index, index+1, index+4, index+9, index+16, ...
```

**Example** — same setup:

```
Insert "cat" → index 3 empty → store at 3
Insert "act" → index 3 taken → try 3+1=4 → empty → store at 4
Insert "tac" → index 3 taken → try 3+1=4 taken → try 3+4=7 → wrap → index 0 → store at 0

index:  0      1     2     3      4      5     6
      [tac]  [ ]   [ ]  [cat]  [act]  [ ]   [ ]
```

Keys spread out instead of clumping. This avoids primary clustering, but keys that start at the same index still follow the same probe sequence — called **secondary clustering**, which is much less severe.

**Note:** use a prime number as the array size to guarantee the probe sequence covers all slots.

---

### Double Hashing

Instead of a fixed step pattern, double hashing uses a **second hash function** to compute the step size for each key individually.

```
step = secondHash(key)
probe sequence: index, index+step, index+2·step, index+3·step, ...
```

Because different keys get different step sizes, two keys that start at the same index will diverge onto completely different probe sequences. This eliminates both primary and secondary clustering. It's the most effective open addressing strategy, though it's slightly more complex to implement since you need two well-designed hash functions.

---

### Summary

| Strategy | Step pattern | Clustering |
|---|---|---|
| Linear probing | +1, +1, +1 | Primary (bad) |
| Quadratic probing | +1², +2², +3² | Secondary (less bad) |
| Double hashing | step size from second hash | Minimal |
