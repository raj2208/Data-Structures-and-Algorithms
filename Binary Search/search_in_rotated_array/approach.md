# Approach — Find Pivot, Then Binary Search

## The core idea

A rotated sorted array is made of two sorted halves joined at a pivot. If we know where the pivot is, we know exactly which half the key belongs to — and then it's just a plain binary search on that half.

```
[7, 8, 1, 3, 5]
 ↑  ↑           ← left sorted half (7, 8)
       ↑  ↑  ↑  ← right sorted half (1, 3, 5)
    pivot = index 1 (value 8, the largest element)
```

## Step 1 — Find the pivot

The pivot is the index of the **largest element** — the point where the array "dips" (where `arr[pivot] > arr[pivot+1]`).

We find it with binary search:
- If `arr[mid] >= arr[0]`, then `mid` is in the left (larger) sorted half → pivot is further right → `s = mid + 1`
- Otherwise `mid` is in the right (smaller) half → pivot is at `mid` or to its left → `e = mid - 1`
- If `arr[mid] > arr[mid+1]`, we found the pivot directly → return `mid`
- If no dip is ever found, the array isn't rotated at all → return `-1`

## Step 2 — Decide which half to search

Once we have the pivot index:
- Left half: indices `0` to `pivot`
- Right half: indices `pivot+1` to `n-1`

To decide which half K is in:
- If `K >= arr[0]`, K is in the left half (since left half starts at arr[0])
- Otherwise K is in the right half

## Step 3 — Standard binary search

Run a normal binary search on whichever half was chosen. If the array wasn't rotated (pivot = -1), just search the whole array.

## Why this works

Splitting at the pivot gives us two clean sorted subarrays. Binary search requires a sorted input — once we isolate the right half, we're back to the standard problem.
