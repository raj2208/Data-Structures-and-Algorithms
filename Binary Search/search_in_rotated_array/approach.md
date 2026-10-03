# Approach — Find Minimum (Pivot), Then Binary Search

## The core idea

A rotated sorted array is made of two sorted halves. The **minimum element** is exactly where the right half begins — it's the pivot point. If we find its index, we know both halves perfectly, and searching becomes a plain binary search on whichever half contains K.

```
[7, 8, 1, 3, 5]
 ↑  ↑           ← left sorted half  (indices 0..1)
       ↑  ↑  ↑  ← right sorted half (indices 2..4)
       ^
    minimum = index 2 → this IS the pivot
```

> This reuses the exact same logic from `find_minimum_rotated_array`. The `findMinIndex` function is identical — we're just using its result differently here (to split the array for search instead of returning the value).

## Step 1 — Find the index of the minimum (pivot)

Same binary search as find_minimum_rotated_array:
- If the current window is already sorted (`arr[s] <= arr[e]`), minimum is at `s` — return immediately
- If `arr[mid] >= arr[0]`, mid is in the left (larger) half → minimum is further right → `s = mid + 1`
- Otherwise mid is in the right (smaller) half → minimum is at mid or left → `e = mid`

## Step 2 — Decide which half K is in

- Left half: indices `0` to `pivot - 1`
- Right half: indices `pivot` to `n - 1`

To pick the right half:
- If `K >= arr[0]`, K is in the left half (left half contains the larger values starting at arr[0])
- Otherwise K is in the right half

If pivot = 0, the array isn't rotated — search the whole thing.

## Step 3 — Standard binary search on the chosen half
