> **Trick:** Find the first and last occurrence using binary search, then the count is just `last - first + 1`. No need to count anything manually.

## Approach

Reuse the two binary search helpers from the first-and-last-position problem. If `firstOccurrence` returns `-1`, K isn't in the array — return `0`. Otherwise `last - first + 1` gives the exact count because the array is sorted, meaning all occurrences of K are contiguous.

## Code

```cpp
int firstOccurrence(vector<int>& arr, int n, int k) {
    int s = 0, e = n - 1, result = -1;
    while (s <= e) {
        int m = s + (e - s) / 2;
        if (arr[m] == k) { result = m; e = m - 1; }  // save, go left
        else if (arr[m] > k) e = m - 1;
        else s = m + 1;
    }
    return result;
}

int lastOccurrence(vector<int>& arr, int n, int k) {
    int s = 0, e = n - 1, result = -1;
    while (s <= e) {
        int m = s + (e - s) / 2;
        if (arr[m] == k) { result = m; s = m + 1; }  // save, go right
        else if (arr[m] > k) e = m - 1;
        else s = m + 1;
    }
    return result;
}

int totalOccurrences(vector<int>& arr, int n, int k) {
    int first = firstOccurrence(arr, n, k);

    // if K isn't in the array at all, return 0
    if (first == -1) return 0;

    int last = lastOccurrence(arr, n, k);

    // all occurrences are contiguous since array is sorted
    // so last index - first index + 1 = total count
    return last - first + 1;
}
```

## Dry Run

Input: `ARR = [1, 1, 2, 2, 2, 3]`, `K = 2`

```
firstOccurrence  →  2  (index 2)
lastOccurrence   →  4  (index 4)

count = 4 - 2 + 1 = 3 ✓
```

Input: `ARR = [1, 1, 2, 2, 2, 3]`, `K = 5`

```
firstOccurrence  →  -1
→ return 0 ✓
```

## Complexity

**Time:** O(log n) — two binary searches, both O(log n), and one subtraction.

**Space:** O(1) — no extra memory used.

## Key Pattern

When you already know first and last occurrence, counting is free — it's just arithmetic. This is a good example of building on a solved subproblem rather than solving from scratch.
