> **Trick:** Run binary search twice. When you find K, instead of returning immediately — save the index and keep searching left (for first) or right (for last).

## Approach

Two helper functions, each a small variation of binary search. When the target is found, record the index but don't stop — continue shrinking the window toward the boundary you're looking for. The main function calls both and returns the results as a pair.

## Code

```cpp
int firstOccurrence(vector<int>& arr, int n, int k) {
    int s = 0, e = n - 1;
    int result = -1;

    while (s <= e) {
        int m = s + (e - s) / 2;

        if (arr[m] == k) {
            result = m;  // found K, but there might be an earlier one to the left
            e = m - 1;   // keep searching left half
        } else if (arr[m] > k) {
            e = m - 1;
        } else {
            s = m + 1;
        }
    }

    return result;
}

int lastOccurrence(vector<int>& arr, int n, int k) {
    int s = 0, e = n - 1;
    int result = -1;

    while (s <= e) {
        int m = s + (e - s) / 2;

        if (arr[m] == k) {
            result = m;  // found K, but there might be a later one to the right
            s = m + 1;   // keep searching right half
        } else if (arr[m] > k) {
            e = m - 1;
        } else {
            s = m + 1;
        }
    }

    return result;
}

pair<int, int> firstAndLastPosition(vector<int>& arr, int n, int k) {
    int first = firstOccurrence(arr, n, k);
    int last = lastOccurrence(arr, n, k);
    return {first, last};  // if K not found, both are -1
}
```

## Dry Run

Input: `ARR = [0, 0, 1, 1, 2, 2, 2, 2]`, `K = 2`

**firstOccurrence:**
```
s=0, e=7  →  m=3  arr[3]=1   1 < 2  →  s=4
s=4, e=7  →  m=5  arr[5]=2   found! result=5, e=4
s=4, e=4  →  m=4  arr[4]=2   found! result=4, e=3
s=4, e=3  →  s > e, stop
→ return 4
```

**lastOccurrence:**
```
s=0, e=7  →  m=3  arr[3]=1   1 < 2  →  s=4
s=4, e=7  →  m=5  arr[5]=2   found! result=5, s=6
s=6, e=7  →  m=6  arr[6]=2   found! result=6, s=7
s=7, e=7  →  m=7  arr[7]=2   found! result=7, s=8
s=8, e=7  →  s > e, stop
→ return 7
```

Result: `{4, 7}` ✓

## Complexity

**Time:** O(log n) — two independent binary searches, each halving the array. Total is 2 × O(log n) which is still O(log n).

**Space:** O(1) — only a handful of integer variables, no extra memory.

## Key Pattern

When binary search needs to find a boundary (first/last/leftmost/rightmost), the trick is: don't return when you find the target. Save it and keep narrowing toward the boundary you want. This pattern appears in many variants — floor, ceil, count occurrences, etc.
