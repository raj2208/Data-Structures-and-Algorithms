# Find Minimum in Rotated Sorted Array

## Problem Statement

A sorted array of unique elements has been rotated between 1 and `n` times. Given the rotated array `nums`, return the **minimum element**.

You must solve it in `O(log n)` time.

A rotation moves the last element to the front. For example:
- `[1,2,3,4,5]` rotated 3 times → `[3,4,5,1,2]`
- `[1,2,3,4,5]` rotated 5 times → `[1,2,3,4,5]` (back to original)

---

## Examples

**Example 1**
```
Input:  nums = [3, 4, 5, 1, 2]
Output: 1

Original array [1,2,3,4,5] was rotated 3 times.
```

**Example 2**
```
Input:  nums = [4, 5, 6, 7, 0, 1, 2]
Output: 0

Original array [0,1,2,4,5,6,7] was rotated 4 times.
```

**Example 3 — No effective rotation**
```
Input:  nums = [11, 13, 15, 17]
Output: 11

Rotated n times = back to sorted order, minimum is first element.
```

---

## Constraints

- `n == nums.length`
- `1 <= n <= 5000`
- `-5000 <= nums[i] <= 5000`
- All elements are unique
- Array was originally sorted in ascending order and rotated 1 to n times

---

## Test Cases

| Input | Expected Output |
|-------|----------------|
| `[3, 4, 5, 1, 2]` | `1` |
| `[4, 5, 6, 7, 0, 1, 2]` | `0` |
| `[11, 13, 15, 17]` | `11` |
| `[2, 1]` | `1` |
| `[1]` | `1` |
| `[5, 1, 2, 3, 4]` | `1` |
