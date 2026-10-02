> **Trick:** `m = s + (e - s) / 2` gives the exact same midpoint as `(s + e) / 2` but never causes an integer overflow — which the naive version can.

## Why does this exist?

The standard midpoint formula `(s + e) / 2` has a subtle bug: **addition can overflow**.

If `s` and `e` are both large (say, close to `INT_MAX` which is ~2.1 billion), then `s + e` exceeds what a 32-bit integer can hold. The result wraps around to a negative number, and your midpoint becomes garbage.

```
s = 2,000,000,000
e = 2,000,000,000
s + e = 4,000,000,000  ← overflows INT_MAX (2,147,483,647)
```

The fix: `s + (e - s) / 2`

- `e - s` is always a small positive number (just the gap between the pointers)
- Adding that gap to `s` can never exceed `e`
- So the result is always within a safe range

Mathematically they're identical:
```
s + (e - s) / 2
= s + e/2 - s/2
= s/2 + e/2
= (s + e) / 2
```

Same answer, no overflow risk.

In practice, LeetCode constraints are small enough that it won't happen — but in a real system or a Google interview, using the safe version signals that you know this gotcha. It's worth writing it this way by habit.

## Code

```cpp
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int s = 0;
        int e = nums.size() - 1;

        while (s <= e) {
            // safe midpoint: avoids overflow when s and e are both large
            int m = s + (e - s) / 2;

            if (nums[m] == target) {
                return m;
            } else if (nums[m] > target) {
                e = m - 1;
            } else {
                s = m + 1;
            }
        }

        return -1;
    }
};
```

## Dry Run

Input: `nums = [-1, 0, 3, 5, 9, 12]`, `target = 5`

```
s=0, e=5  →  m = 0 + (5-0)/2 = 2  nums[2]=3   3 < 5  →  s = 3
s=3, e=5  →  m = 3 + (5-3)/2 = 4  nums[4]=9   9 > 5  →  e = 3
s=3, e=3  →  m = 3 + (3-3)/2 = 3  nums[3]=5   5 == 5 →  return 3
```

## Complexity

**Time:** O(log n) — identical to the standard version, same halving logic.

**Space:** O(1) — still just three integers.

## Key Pattern

Always use `s + (e - s) / 2` over `(s + e) / 2`. It costs nothing extra and eliminates a real class of bug. In interviews, using this version unprompted shows you think about edge cases at scale.
