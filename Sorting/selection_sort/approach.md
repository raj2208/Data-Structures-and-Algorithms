# Selection Sort — Approach

## Concept

Selection sort divides the array into two parts: a sorted left portion and an unsorted right portion. On each pass, it finds the smallest element in the unsorted portion and swaps it into its correct position at the boundary.

After `i` passes, the first `i` elements are sorted and will never move again.

## Visual

```
[5, 3, 1, 4, 2]   initial

Pass 1: find min in [5,3,1,4,2] → 1 at index 2 → swap with index 0
[1, 3, 5, 4, 2]

Pass 2: find min in [3,5,4,2] → 2 at index 4 → swap with index 1
[1, 2, 5, 4, 3]

Pass 3: find min in [5,4,3] → 3 at index 4 → swap with index 2
[1, 2, 3, 4, 5]

Pass 4: find min in [4,5] → 4 already in place
[1, 2, 3, 4, 5]  done
```

## Key Idea

At each step `i`, scan from `i` to `n-1` to find the index of the minimum element, then swap it with `arr[i]`. The outer loop runs `n-1` times since the last element is automatically in place.

## Complexity

**Time:** O(n²) — the inner loop always scans the full remaining unsorted portion, even if already sorted. No early exit.

**Space:** O(1) — sorts in-place, only a few variables.

## When to Use

Selection sort is rarely used in practice due to O(n²) time. It makes at most `n-1` swaps though, which is useful when writes are expensive (e.g., flash memory).
