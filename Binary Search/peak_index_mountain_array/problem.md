# Peak Index in a Mountain Array

## Problem Statement

Given an integer mountain array `arr` of length `n`, return the **index of the peak element**.

A mountain array strictly increases up to the peak, then strictly decreases after it. The peak is the largest element.

Solve it in `O(log n)` time.

---

## Examples

**Example 1**
```
Input:  arr = [0, 1, 0]
Output: 1

1 is the peak — larger than both neighbours.
```

**Example 2**
```
Input:  arr = [0, 2, 1, 0]
Output: 1

2 is the peak.
```

**Example 3**
```
Input:  arr = [0, 10, 5, 2]
Output: 1

10 is the peak.
```

---

## Constraints

- `3 <= arr.length <= 10^5`
- `0 <= arr[i] <= 10^6`
- `arr` is guaranteed to be a mountain array (peak always exists)

---

## Test Cases

| Input | Expected Output |
|-------|----------------|
| `[0, 1, 0]` | `1` |
| `[0, 2, 1, 0]` | `1` |
| `[0, 10, 5, 2]` | `1` |
| `[1, 3, 5, 4, 2]` | `2` |
| `[0, 1, 2, 3, 1]` | `3` |
| `[3, 5, 3, 2, 0]` | `1` |
