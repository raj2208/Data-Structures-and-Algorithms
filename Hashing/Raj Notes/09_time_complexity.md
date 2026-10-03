# Time Complexity of Hash Maps

## The setup

Say you have an array of `n` words, and `k` is the average length of each word.

When you insert a word into a hash map, two things happen:
1. Compute the hash code — this means looking at **every character** of the word
2. Drop it into the right bucket — this is just array indexing, instant

Step 1 takes O(k) time. So technically, inserting a single word is **O(k)**, not O(1).

This is correct. You are not wrong.

---

## So why do people say hash maps are O(1)?

Here's the intuition:

When you say something is O(1), it means the time doesn't grow as your input grows. "Constant time."

Now ask: as you add more and more words to your array (n gets bigger and bigger), does k — the average word length — also get bigger?

**No. It doesn't.**

English words don't get longer just because you have more of them. Whether you have 100 words or 10 million words, the average word length stays roughly the same — maybe 5, 6, 7 characters. `k` is a fixed number. It doesn't scale with `n`.

---

## The key insight

When analyzing time complexity, we care about what grows as input grows. We use `n` as the "thing that grows."

Since `k` doesn't change as `n` changes, `k` is just a **constant**. And O(constant) = O(1).

So O(k) becomes O(1) — not because k literally equals 1, but because k doesn't move. It's fixed. It's as if you wrote O(6) — that's just O(1).

---

## Visualizing it

Imagine n grows from 100 to 1,000,000:

```
n = 100        k ≈ 6    →   work per insert ≈ 6 steps
n = 1,000      k ≈ 6    →   work per insert ≈ 6 steps
n = 1,000,000  k ≈ 6    →   work per insert ≈ 6 steps
```

The work per insert never changed. It stayed at 6. That's what O(1) means — constant, flat, doesn't grow.

---

## When n >> k makes this clearest

If n is much much larger than k — say n = 1,000,000 and k = 6 — then k is essentially invisible in the analysis. The total work to insert all n words is:

```
n * k  =  1,000,000 * 6  =  6,000,000  →  O(n)
```

And per word, that's O(k) = O(6) = O(1).

The "n >> k" framing just means: once n is large enough, k is so comparatively tiny that it genuinely doesn't factor into how the algorithm scales.

---

## The honest summary

| Operation | Technical complexity | Why we call it O(1) |
|---|---|---|
| Insert a word | O(k) | k is bounded, doesn't grow with n |
| Lookup a word | O(k) | same reason |
| Delete a word | O(k) | same reason |

For integer keys (like `unordered_map<int, int>`), k = 1 always, so it's literally O(1) with no asterisk.
