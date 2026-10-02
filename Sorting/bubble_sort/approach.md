# Bubble Sort — Approach

## Concept

Bubble sort repeatedly steps through the array, compares adjacent elements, and swaps them if they're in the wrong order. Each full pass "bubbles" the largest unsorted element to its correct position at the end.

After `i` passes, the last `i` elements are sorted and don't need to be visited again.

## Visual

```
[5, 3, 1, 4, 2]   initial

Pass 1: compare neighbours, swap if out of order
5>3 swap → [3,5,1,4,2]
5>1 swap → [3,1,5,4,2]
5>4 swap → [3,1,4,5,2]
5>2 swap → [3,1,4,2,5]   ← 5 is in place

Pass 2:
3>1 swap → [1,3,4,2,5]
3<4 ok
4>2 swap → [1,3,2,4,5]   ← 4 is in place

Pass 3:
1<3 ok
3>2 swap → [1,2,3,4,5]   ← 3 is in place

Array sorted!
```

## Key Idea

The outer loop runs up to `n-1` times. The inner loop compares `arr[j]` with `arr[j+1]` for `j` from 0 to `n-i-2` (shrinking each pass since the end is already sorted).

**Optimization:** track a `swapped` flag. If a full inner pass makes zero swaps, the array is already sorted — exit early. This makes best case O(n) for already-sorted input.

## Complexity

**Time:** O(n²) worst/average — compares every pair. O(n) best — with the swapped-flag optimization on an already-sorted array.

**Space:** O(1) — in-place, just a swap variable.

## When to Use

Bubble sort is mostly a teaching tool. The swapped-flag optimization makes it decent for nearly-sorted data, but for general use, prefer merge sort or quicksort.
