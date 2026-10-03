# Search in Rotated Sorted Array

## Problem Statement

You are given a sorted array `ARR` of `N` elements that has been rotated at some unknown pivot point. You are also given an integer `K`.

Find the index at which `K` is present in `ARR`. If `K` is not present, return `-1`.

**Notes:**
- No duplicate elements
- Array is rotated only in the right direction
- If K is not found, return -1

**Example:**
`ARR = [1, 3, 5, 7, 8]` rotated at index 3 → `ARR = [7, 8, 1, 3, 5]`

---

## Examples

**Example 1**
```
Input:  ARR = [7, 8, 1, 3, 5],  K = 3
Output: 3

3 is at index 3.
```

**Example 2**
```
Input:  ARR = [3, 5, 7, 8, 1],  K = 8
Output: 3
```

**Example 3 — Key not present**
```
Input:  ARR = [7, 8, 1, 3, 5],  K = 10
Output: -1
```

**Example 4 — No rotation**
```
Input:  ARR = [1, 3, 5, 7, 8],  K = 5
Output: 2
```

---

## Constraints

- `1 <= N <= 10^5`
- `-10^9 <= ARR[i], K <= 10^9`
- All elements are unique
- Array was originally sorted in ascending order

---

## Test Cases

| Input | K | Expected Output |
|-------|---|----------------|
| `[7, 8, 1, 3, 5]` | 3 | 3 |
| `[3, 5, 7, 8, 1]` | 8 | 3 |
| `[7, 8, 1, 3, 5]` | 10 | -1 |
| `[1, 3, 5, 7, 8]` | 5 | 2 |
| `[5]` | 5 | 0 |
