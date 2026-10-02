> **Trick:** Maintain two pointers `s` and `e`. Each iteration, check the midpoint — if it's not the target, cut the search space in half by moving either `s` or `e`.

## Approach

Start with `s = 0` and `e = last index`. Compute the midpoint each iteration. If `nums[m] == target`, return `m`. If `nums[m] > target`, the target must be in the left half so move `e = m - 1`. If `nums[m] < target`, the target is in the right half so move `s = m + 1`. If the pointers cross without finding it, return `-1`.

## Code

```cpp
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int s = 0;
        int e = nums.size() - 1;

        while (s <= e) {
            int m = (s + e) / 2; // midpoint of current search window

            if (nums[m] == target) {
                return m; // found it
            } else if (nums[m] > target) {
                e = m - 1; // target is in the left half, shrink right boundary
            } else {
                s = m + 1; // target is in the right half, shrink left boundary
            }
        }

        return -1; // search space exhausted, target not in array
    }
};
```

## Dry Run

Input: `nums = [-1, 0, 3, 5, 9, 12]`, `target = 9`

```
s=0, e=5  →  m=2  nums[2]=3   3 < 9  →  s = 3
s=3, e=5  →  m=4  nums[4]=9   9 == 9 →  return 4
```

Input: `nums = [-1, 0, 3, 5, 9, 12]`, `target = 2`

```
s=0, e=5  →  m=2  nums[2]=3   3 > 2  →  e = 1
s=0, e=1  →  m=0  nums[0]=-1 -1 < 2  →  s = 1
s=1, e=1  →  m=1  nums[1]=0   0 < 2  →  s = 2
s=2, e=1  →  s > e, exit loop
return -1
```

## Complexity

**Time:** O(log n) — each iteration cuts the search space in half, so we need at most log₂(n) steps.

**Space:** O(1) — just three integer variables, no extra memory used.

## Key Pattern

Whenever the array is sorted and you need to find something, think binary search. The key is always: after checking the midpoint, which half can you safely throw away? That question determines how you move your pointers.
