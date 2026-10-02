> **Trick:** Compare adjacent pairs and swap if out of order. Each full pass floats the largest unsorted element to its final position. Stop early if a pass makes zero swaps.

## Approach

Run an outer loop `n-1` times. In each pass, walk through adjacent pairs from index 0 to `n-i-2` (the last `i` elements are already sorted). If any adjacent pair is out of order, swap them and set a `swapped` flag. If the flag stays false after a full pass, the array is sorted — exit early.

## Code

```cpp
class Solution {
public:
    void bubbleSort(vector<int>& arr) {
        int n = arr.size();
        for (int i = 0; i < n - 1; i++) {
            bool swapped = false;
            // each pass pushes the largest remaining element to arr[n-i-1]
            for (int j = 0; j < n - i - 1; j++) {
                if (arr[j] > arr[j + 1]) {
                    swap(arr[j], arr[j + 1]);
                    swapped = true;
                }
            }
            // no swaps means the array is already sorted
            if (!swapped) break;
        }
    }
};
```

## Dry Run

Input: `[5, 3, 1, 4, 2]`

```
Pass 1 (i=0): compare pairs up to index 3
  5>3 swap → [3,5,1,4,2]
  5>1 swap → [3,1,5,4,2]
  5>4 swap → [3,1,4,5,2]
  5>2 swap → [3,1,4,2,5]   swapped=true

Pass 2 (i=1): compare pairs up to index 2
  3>1 swap → [1,3,4,2,5]
  3<4 ok
  4>2 swap → [1,3,2,4,5]   swapped=true

Pass 3 (i=2): compare pairs up to index 1
  1<3 ok
  3>2 swap → [1,2,3,4,5]   swapped=true

Pass 4 (i=3): compare pairs up to index 0
  1<2 ok                   swapped=false → break ✓
```

## Complexity

**Time:** O(n²) worst/average — every adjacent pair compared on each pass. O(n) best — if array is already sorted, the swapped flag catches it after one pass.

**Space:** O(1) — in-place with just a boolean flag and swap variable.

## Key Pattern

Bubble sort is "push the largest unsorted element to the end, one pass at a time." The swapped-flag early exit is the only practical optimization — without it, it's always O(n²). This pattern of "one pass moves one element to its final place" appears in other algorithms too (like selection sort going the other direction).
