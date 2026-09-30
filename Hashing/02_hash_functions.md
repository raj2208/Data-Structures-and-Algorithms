# Hash Functions

## What Makes a Hash Function "Good"?

A hash function takes a key and returns an integer index in range `[0, m-1]` where `m` is the table size. Four properties matter:

1. **Deterministic** — same key always produces the same index
2. **Uniform distribution** — keys spread evenly across all slots, minimizing collisions
3. **Fast to compute** — O(key length) at most; ideally O(1) for fixed-size keys
4. **Avalanche effect** — a small change in the key produces a very different hash value (helps with uniformity)

---

## Universal Hashing (Theory Only)

If the hash function is fixed and known, an adversary can deliberately craft inputs where all keys collide — degrading O(1) to O(n). This is called a **hash flooding attack**.

The theoretical solution is **universal hashing**: pick the hash function _randomly_ at startup from a large family of functions. An attacker who doesn't know which function was chosen can't craft collisions.

The practical takeaway: **randomize your hash to defend against adversarial input**. You don't need to implement this from scratch in an interview — just know the concept and why it matters.

---

## What's Next

- [03_collision_resolution.md](03_collision_resolution.md) — What happens when two keys hash to the same slot?
