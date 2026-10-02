# Total Number of Occurrences in a Sorted Array

## Problem Statement

Given a sorted array `ARR` of `N` elements and an integer `K`, return the **total number of times K appears** in the array.

If K is not present, return `0`.

---

## Examples

**Example 1**
```
Input:  ARR = [1, 1, 2, 2, 2, 3],  K = 2
Output: 3

2 appears at indices 2, 3, 4 → 3 times.
```

**Example 2 — Not present**
```
Input:  ARR = [1, 1, 2, 2, 2, 3],  K = 5
Output: 0

5 does not exist in the array.
```

**Example 3 — All same**
```
Input:  ARR = [4, 4, 4, 4],  K = 4
Output: 4

Every element is K.
```

---

## Constraints

- `1 <= N <= 10^5`
- `0 <= ARR[i], K <= 10^5`
- Array is sorted in ascending order

---

## Test Cases

| ARR | K | Expected Output |
|-----|---|----------------|
| `[1, 1, 2, 2, 2, 3]` | `2` | `3` |
| `[1, 1, 2, 2, 2, 3]` | `5` | `0` |
| `[4, 4, 4, 4]` | `4` | `4` |
| `[1, 2, 3, 4, 5]` | `3` | `1` |
| `[1]` | `1` | `1` |
| `[1]` | `2` | `0` |
