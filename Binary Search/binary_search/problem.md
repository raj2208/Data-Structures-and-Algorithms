# Binary Search

## Problem Statement

Given an array of integers `nums` sorted in ascending order, and an integer `target`, return the index of `target` in `nums`. If it does not exist, return `-1`.

You must write an algorithm with `O(log n)` runtime complexity.

---

## Examples

**Example 1**
```
Input:  nums = [-1, 0, 3, 5, 9, 12], target = 9
Output: 4

9 exists in nums at index 4.
```

**Example 2**
```
Input:  nums = [-1, 0, 3, 5, 9, 12], target = 2
Output: -1

2 does not exist in nums.
```

**Example 3 — Single element**
```
Input:  nums = [5], target = 5
Output: 0

Only one element and it matches.
```

---

## Constraints

- `1 <= nums.length <= 10^4`
- `-10^4 < nums[i], target < 10^4`
- All integers in `nums` are unique
- `nums` is sorted in ascending order

---

## Test Cases

| Input | Target | Expected Output |
|-------|--------|----------------|
| `[-1, 0, 3, 5, 9, 12]` | `9` | `4` |
| `[-1, 0, 3, 5, 9, 12]` | `2` | `-1` |
| `[5]` | `5` | `0` |
| `[5]` | `3` | `-1` |
| `[1, 2, 3, 4, 5]` | `1` | `0` |
| `[1, 2, 3, 4, 5]` | `5` | `4` |
