> **Trick:** Find the index of the minimum element — that's the pivot, the exact start of the right sorted half. Then compare K with arr[0] to pick which half to binary search. This reuses the findMin logic from `find_minimum_rotated_array` almost unchanged.

## Approach

`findMinIndex` returns the index of the smallest element, which splits the array into two sorted halves. Then decide which half K belongs to and run standard binary search on it.

## Code

```cpp
class Solution {
public:
    // same logic as find_minimum_rotated_array — returns index instead of value
    int findMinIndex(vector<int>& arr, int n) {
        int s = 0, e = n - 1;
        while (s <= e) {
            // window is already sorted — minimum is at the left end
            if (arr[s] <= arr[e])
                return s;
            int m = s + (e - s) / 2;
            // mid is in the left (larger) half — minimum is to the right
            if (arr[m] >= arr[0])
                s = m + 1;
            // mid is in the right (smaller) half — minimum is at m or to its left
            else
                e = m;
        }
        return 0;
    }

    int binarySearch(vector<int>& arr, int s, int e, int key) {
        while (s <= e) {
            int m = s + (e - s) / 2;
            if (arr[m] == key) return m;
            else if (arr[m] > key) e = m - 1;
            else s = m + 1;
        }
        return -1;
    }

    int findPosition(vector<int>& arr, int n, int k) {
        int pivot = findMinIndex(arr, n); // pivot = start of right sorted half

        // pivot = 0 means no rotation — search the whole array
        if (pivot == 0)
            return binarySearch(arr, 0, n - 1, k);

        // k >= arr[0] means k belongs to the left sorted half
        if (k >= arr[0])
            return binarySearch(arr, 0, pivot - 1, k);

        // otherwise k is in the right sorted half (starts at pivot)
        return binarySearch(arr, pivot, n - 1, k);
    }
};
```

## Dry Run

Input: `arr = [7, 8, 1, 3, 5]`, `k = 3`

```
findMinIndex:
  s=0, e=4  arr[0]=7 > arr[4]=5 → not sorted
  m=2  arr[2]=1 < arr[0]=7 → right half → e=2

  s=0, e=2  arr[0]=7 > arr[2]=1 → not sorted
  m=1  arr[1]=8 >= arr[0]=7 → left half → s=2

  s=2, e=2  arr[2]=1 <= arr[2]=1 → sorted → return 2

pivot = 2

k=3, arr[0]=7 → 3 < 7 → search right half: indices 2..4
binarySearch(arr, 2, 4, 3):
  m=3  arr[3]=3 == 3 → return 3 ✓
```

Input: `arr = [1, 3, 5, 7, 8]`, `k = 5` (no rotation)

```
findMinIndex:
  s=0, e=4  arr[0]=1 <= arr[4]=8 → sorted → return 0

pivot = 0 → search whole array
binarySearch(arr, 0, 4, 5) → return 2 ✓
```

## Complexity

**Time:** O(log n) — findMinIndex is O(log n), binary search on the half is O(log n).

**Space:** O(1) — only index variables.

## Key Pattern

The minimum element in a rotated sorted array is always the exact boundary between the two sorted halves. Finding it once gives you everything you need to reduce the search to a standard binary search — no special casing needed mid-search.
