# Load Factor and Rehashing

## What is load factor?

Load factor is a simple ratio:

```
load factor = number of entries / number of buckets
```

If you have 7 entries in a bucket array of size 10, your load factor is 0.7.

It's just a measure of how full your hash map is.

---

## Why does it matter?

As the hash map fills up, collisions become more frequent. More keys have to share buckets, chains get longer (separate chaining) or probing sequences stretch out (open addressing). Either way, operations that should be O(1) start taking longer.

Think of it like a parking lot. When it's 20% full, you find a spot instantly. When it's 95% full, you're circling around for a while.

---

## Why 0.7 specifically?

0.7 is the widely accepted sweet spot — empirically tested across many hash map implementations.

- **Below 0.7**: collisions are rare, buckets are spread out, operations stay close to O(1)
- **Above 0.7**: collisions pile up fast, performance degrades noticeably
- **At 1.0**: every bucket is occupied — open addressing completely breaks down, separate chaining has long chains everywhere

0.7 isn't a hard law of nature, but it's the threshold where the tradeoff between memory usage and performance is optimal for most real-world workloads.

---

## What is rehashing?

When the load factor crosses 0.7, the hash map needs more space. Here's what happens:

### Step 1 — Grow the array

A new, larger bucket array is created — typically **double** the size of the old one.

```
Old array: size 10  →  New array: size 20
```

### Step 2 — Rehash every existing entry

You can't just copy entries over to the same indices. The compression function is `hashCode % N`, and N just changed. Every key's index is now different.

So you go through every entry in the old array and **recompute its hash** against the new size to find its new bucket.

```
"cat" was at index 3 in size-10 array  (hashCode("cat") % 10 = 3)
"cat" goes to index 13 in size-20 array  (hashCode("cat") % 20 = 13)
```

### Step 3 — Discard the old array

Old array is thrown away. The new larger array is now the hash map.

---

## Visualizing the whole flow

```
Start: 10 buckets, 0 entries. Load = 0/10 = 0.0

Insert 7 keys...

Load = 7/10 = 0.7  ← threshold hit!

→ Create new array of size 20
→ Rehash all 7 entries into new positions
→ Load = 7/20 = 0.35  ← back to comfortable territory

Keep inserting...

Load = 14/20 = 0.7  ← threshold hit again!

→ Create new array of size 40
→ Rehash all 14 entries...
```

---

## Isn't rehashing expensive?

Yes — a single rehash is O(n), since you touch every existing entry. But it happens rarely. As the array doubles each time, you need fewer and fewer rehashes relative to how many insertions you do.

The math works out so that the **amortized cost** per insertion is still O(1). You pay a big cost once in a while, but spread across all insertions, each one costs a constant amount on average.

This is the same idea as how a dynamic array (like `vector` in C++) stays O(1) amortized even though it occasionally has to copy everything when it doubles.

---

## Summary

| Term | What it means |
|---|---|
| Load factor | entries / buckets — how full the map is |
| Threshold (0.7) | the point where performance starts to hurt |
| Rehashing | grow the array, recompute every key's index for the new size |
| Why O(1) still holds | rehashing is rare; amortized across all insertions it averages out |
