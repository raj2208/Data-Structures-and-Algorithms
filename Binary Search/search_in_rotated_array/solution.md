> **Trick:** Find the pivot (index of the largest element) first. That splits the array into two clean sorted halves. Then decide which half K lives in and run a standard binary search on just that half.

## Approach

Three steps:
1. Binary search for the pivot — the point where `arr[pivot] > arr[pivot+1]`
2. Compare K with `arr[0]` to pick the correct half
3. Binary search on that half

## Code

```cpp
class Solution {
public:
    int findPivot(vector<int>& arr, int n) {
        int s = 0, e = n - 1;
        while (s <= e) {
            int m = s + (e - s) / 2;
            // found the dip — this is the pivot
            if (m < n - 1 && arr[m] > arr[m + 1])
                return m;
            // mid is in the left (larger) half — pivot is further right
            if (arr[m] >= arr[0])
                s = m + 1;
            // mid is in the right (smaller) half — pivot is to the left
            else
                e = m - 1;
        }
        return -1; // no rotation, array is already sorted
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
        int pivot = findPivot(arr, n);

        // no rotation — search the whole array
        if (pivot == -1)
            return binarySearch(arr, 0, n - 1, k);

        // k >= arr[0] means k is in the left sorted half
        if (k >= arr[0])
            return binarySearch(arr, 0, pivot, k);

        // otherwise k is in the right sorted half
        return binarySearch(arr, pivot + 1, n - 1, k);
    }
};
```

## Dry Run

Input: `arr = [7, 8, 1, 3, 5]`, `k = 3`

```
findPivot:
  s=0, e=4  m=2  arr[2]=1, arr[3]=3  →  1 < 3, no dip. arr[2]=1 < arr[0]=7 → e=1
  s=0, e=1  m=0  arr[0]=7, arr[1]=8  →  7 < 8, no dip. arr[0]=7 >= arr[0]=7 → s=1
  s=1, e=1  m=1  arr[1]=8, arr[2]=1  →  8 > 1, dip found! return 1

pivot = 1

k=3, arr[0]=7  →  3 < 7, search right half: indices 2 to 4

binarySearch(arr, 2, 4, 3):
  s=2, e=4  m=3  arr[3]=3 == 3  →  return 3 ✓
```

Input: `arr = [1, 3, 5, 7, 8]`, `k = 5` (no rotation)

```
findPivot: no dip found → returns -1
binarySearch(arr, 0, 4, 5) → finds 5 at index 2 ✓
```

## Complexity

**Time:** O(log n) — finding the pivot is O(log n), binary search on the half is O(log n), total still O(log n).

**Space:** O(1) — only index variables.

## Key Pattern

When a problem involves a rotated sorted array, finding the pivot first is a clean strategy — it reduces the problem back to standard binary search. The pivot tells you exactly where one sorted half ends and the other begins.
