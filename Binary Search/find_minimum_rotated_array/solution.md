> **Trick:** A rotated sorted array is made of two sorted halves. If `nums[m] >= nums[0]`, the midpoint is in the larger left half — minimum is to the right. Otherwise it's in the smaller right half — minimum is at `m` or to its left.

## Approach

The rotation point splits the array into two sorted halves. The left half contains larger values (from the rotation), the right half contains smaller values including the minimum.

Two key observations:
1. If the current window is already sorted (`nums[s] <= nums[e]`), the minimum is just `nums[s]` — return immediately.
2. Otherwise, compare `nums[m]` with `nums[0]`. If `nums[m] >= nums[0]`, we're in the left (larger) half — minimum is to the right. If `nums[m] < nums[0]`, we're in the right (smaller) half — minimum is at `m` or to its left.

## Code

```cpp
class Solution {
public:
    int findMin(vector<int>& nums) {
        int s = 0;
        int e = nums.size() - 1;

        while (s <= e) {
            // if window is already sorted, minimum is the leftmost element
            if (nums[s] <= nums[e])
                return nums[s];

            int m = s + (e - s) / 2;

            // nums[m] >= nums[0] means m is in the left (larger) sorted half
            // the minimum must be somewhere to the right of m
            if (nums[m] >= nums[0]) {
                s = m + 1;
            }
            // otherwise m is in the right (smaller) sorted half
            // minimum is at m or to its left — don't skip m
            else {
                e = m;
            }
        }

        return -1;
    }
};
```

## Dry Run

Input: `nums = [4, 5, 6, 7, 0, 1, 2]`

```
s=0, e=6  nums[s]=4, nums[e]=2  →  4 > 2, not sorted
          m=3  nums[3]=7  7 >= nums[0]=4  →  left half, s=4

s=4, e=6  nums[s]=0, nums[e]=2  →  0 <= 2, sorted!
          return nums[4] = 0 ✓
```

Input: `nums = [3, 4, 5, 1, 2]`

```
s=0, e=4  nums[s]=3, nums[e]=2  →  3 > 2, not sorted
          m=2  nums[2]=5  5 >= nums[0]=3  →  left half, s=3

s=3, e=4  nums[s]=1, nums[e]=2  →  1 <= 2, sorted!
          return nums[3] = 1 ✓
```

## Complexity

**Time:** O(log n) — each iteration either returns or cuts the window in half.

**Space:** O(1) — only three integer variables.

## Key Pattern

Rotated sorted arrays always consist of two sorted halves joined at a pivot. Binary search still works — you just need to figure out which half you're in at each step. Comparing with `nums[0]` tells you which half the midpoint belongs to, so you always know which direction to search.
