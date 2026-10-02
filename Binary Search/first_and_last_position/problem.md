# First and Last Position of an Element in Sorted Array

## Problem Statement

Given a sorted array `ARR` of `N` elements and an integer `K`, find the **first and last occurrence** of `K` in `ARR` (0-indexed).

If `K` is not present in the array, return `-1 -1` for both positions.

The array may contain duplicate elements.

---

## Examples

**Example 1**
```
Input:  ARR = [0, 1, 1, 5],  K = 1
Output: 1 2

1 first appears at index 1 and last appears at index 2.
```

**Example 2 — Element not present**
```
Input:  ARR = [0, 5, 5, 6, 6, 6],  K = 3
Output: -1 -1

3 does not exist in the array.
```

**Example 3**
```
Input:  ARR = [0, 0, 1, 1, 2, 2, 2, 2],  K = 2
Output: 4 7

2 first appears at index 4 and last appears at index 7.
```

---

## Constraints

- `1 <= T <= 100` (number of test cases)
- `1 <= N <= 5000`
- `0 <= K <= 10^5`
- `0 <= ARR[i] <= 10^5`
- Array is sorted in ascending order
- Time limit: 1 second

---

## Test Cases

| ARR | K | Expected Output |
|-----|---|----------------|
| `[0, 5, 5, 6, 6, 6]` | `3` | `-1 -1` |
| `[0, 0, 1, 1, 2, 2, 2, 2]` | `2` | `4 7` |
| `[0, 1, 1, 5]` | `1` | `1 2` |
| `[1, 1, 1, 1]` | `1` | `0 3` |
| `[1, 2, 3, 4, 5]` | `5` | `4 4` |
| `[1, 2, 3, 4, 5]` | `6` | `-1 -1` |
