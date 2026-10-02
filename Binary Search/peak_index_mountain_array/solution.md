> **Trick:** At any midpoint, compare with the next element. If `arr[m] > arr[m+1]`, you're on the descending side — peak is to the left. If `arr[m] < arr[m+1]`, you're still ascending — peak is to the right. When both neighbours are smaller, you're at the peak.

## Approach

At each midpoint, you're either on the ascending slope, the descending slope, or at the peak. Comparing `arr[m]` with its neighbours tells you which side you're on, and you cut the search space in half accordingly.

- `arr[m] > arr[m+1]` AND `arr[m] > arr[m-1]` → peak found, return `m`
- `arr[m] > arr[m+1]` alone → on the right (descending) side, peak is left → `e = m - 1`
- otherwise → on the left (ascending) side, peak is right → `s = m + 1`

## Code

```cpp
class Solution {
public:
    int peakIndexInMountainArray(vector<int>& arr) {
        int s = 0;
        int e = arr.size() - 1;

        while (s <= e) {
            int m = s + (e - s) / 2;

            // peak: larger than both neighbours
            if (arr[m] > arr[m + 1] && arr[m] > arr[m - 1])
                return m;

            // arr[m] > arr[m+1] means we're on the descending slope
            // the peak must be somewhere to the left
            else if (arr[m] > arr[m + 1])
                e = m - 1;

            // otherwise we're on the ascending slope
            // the peak is still ahead to the right
            else
                s = m + 1;
        }

        return 0;
    }
};
```

## Dry Run

Input: `arr = [1, 3, 5, 4, 2]`

```
s=0, e=4  →  m=2  arr[2]=5  arr[3]=4  arr[1]=3
            5 > 4 AND 5 > 3  →  peak found, return 2 ✓
```

Input: `arr = [0, 1, 2, 3, 1]`

```
s=0, e=4  →  m=2  arr[2]=2  arr[3]=3  arr[1]=1
            2 < 3  →  ascending side, s = 3

s=3, e=4  →  m=3  arr[3]=3  arr[4]=1  arr[2]=2
            3 > 1 AND 3 > 2  →  peak found, return 3 ✓
```

## Complexity

**Time:** O(log n) — classic binary search, halving the array each iteration.

**Space:** O(1) — only a few integer variables.

## Key Pattern

In a mountain array, every element is either on the ascending slope, descending slope, or the peak. Comparing with the next element tells you which region you're in — that's enough to cut the search space in half, just like standard binary search does with a sorted array.
