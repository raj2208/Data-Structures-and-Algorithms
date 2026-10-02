# Approach — Two Binary Searches

The core idea: run binary search **twice** — once to find the first occurrence, once to find the last.

## Why not just find any occurrence and scan outward?

You could find K at some index and then walk left/right to find the boundaries. But that's O(n) in the worst case (e.g., all elements are K). We want O(log n), so we stay in binary search territory for both searches.

## How firstOccurrence works

Standard binary search, but with one change: **when you find K, don't stop**. Save the index, then keep searching the left half. The idea is — there might be an earlier occurrence to the left.

```
found K at index m  →  save m, then set e = m - 1  (go left)
```

The search ends when the window closes. The saved index is the first occurrence.

## How lastOccurrence works

Same thing in reverse: when you find K, save the index and search the **right** half instead.

```
found K at index m  →  save m, then set s = m + 1  (go right)
```

## Putting it together

The main function calls both helpers, packs the results into a `pair<int, int>`, and returns it. If K doesn't exist, both helpers return -1, so the pair is `{-1, -1}` automatically.
