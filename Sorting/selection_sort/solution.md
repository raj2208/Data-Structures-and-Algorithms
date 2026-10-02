> **Trick:** Find the minimum element in the unsorted portion, then swap it to the front. Repeat, shrinking the unsorted portion by one each time.

## Approach

Keep a boundary `i` that moves from left to right. For each `i`, scan everything from `i` to `n-1` to find the smallest element's index, then swap it with `arr[i]`. After each pass, `arr[0..i]` is fully sorted.

## Code

```cpp
class Solution {
public:
    void selectionSort(vector<int>& arr) {
        int n = arr.size();
        for (int i = 0; i < n - 1; i++) {
            int minIdx = i;
            // find the index of the minimum in the unsorted portion
            for (int j = i + 1; j < n; j++) {
                if (arr[j] < arr[minIdx])
                    minIdx = j;
            }
            // place the minimum at the sorted boundary
            swap(arr[i], arr[minIdx]);
        }
    }
};
```

## Dry Run

Input: `[5, 3, 1, 4, 2]`

```
i=0: scan [5,3,1,4,2] → min at index 2 (val 1) → swap(0,2) → [1, 3, 5, 4, 2]
i=1: scan [3,5,4,2]   → min at index 4 (val 2) → swap(1,4) → [1, 2, 5, 4, 3]
i=2: scan [5,4,3]     → min at index 4 (val 3) → swap(2,4) → [1, 2, 3, 4, 5]
i=3: scan [4,5]       → min at index 3 (val 4) → no swap  → [1, 2, 3, 4, 5] ✓
```

## Complexity

**Time:** O(n²) — the inner loop scans the entire remaining unsorted part on every pass, no early exit possible.

**Space:** O(1) — only a few index variables, sorts in-place.

## Key Pattern

Selection sort is "find the next smallest and place it." It always makes at most n-1 swaps (one per outer pass), which is optimal for swap-heavy scenarios — but the inner scan never short-circuits, so it's always O(n²) regardless of input order.
