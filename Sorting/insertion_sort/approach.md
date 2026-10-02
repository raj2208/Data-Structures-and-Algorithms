# Insertion Sort — Approach

## Concept

Insertion sort builds a sorted left portion one element at a time. For each new element, it "inserts" it into its correct position among the already-sorted elements to its left by walking it backwards until it's in place.

Think of how you sort cards in your hand — you pick one card and slide it left past larger cards until it sits correctly.

## Visual

```
[5, 3, 1, 4, 2]   initial

i=1: insert 3 → walk left past 5 → [3, 5, 1, 4, 2]
i=2: insert 1 → walk left past 5, then 3 → [1, 3, 5, 4, 2]
i=3: insert 4 → walk left past 5 only → [1, 3, 4, 5, 2]
i=4: insert 2 → walk left past 5, 4, 3 → [1, 2, 3, 4, 5]
```

## Two Variants

### 1. Shift-based (classic)
Store the element as a `key`, shift all larger elements one step right, then place the key in the gap.

```
key = 3, arr = [5, _, 1, 4, 2]
shift 5 right → [_, 5, 1, 4, 2]
place key     → [3, 5, 1, 4, 2]
```

### 2. Swap-based
Walk the element backwards by swapping it with each left neighbour that's larger. Easier to implement, slightly more swaps but same result.

## Key Idea

The inner loop always moves an element left as long as it's smaller than the element to its left. When it stops, that element is in its correct relative position among everything seen so far.

## Complexity

**Time:** O(n²) worst/average — for a reverse-sorted array, every element has to walk all the way to index 0. O(n) best — on an already-sorted array, the inner loop exits immediately every time.

**Space:** O(1) — in-place.

## When to Use

Insertion sort is efficient on small or nearly-sorted arrays. It's stable (equal elements keep their original order) and adaptive (runs in O(n) on sorted input). Many real-world sorting algorithms (like Timsort) fall back to insertion sort for small subarrays.
