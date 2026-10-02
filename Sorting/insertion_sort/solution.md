> **Trick:** For each element, walk it left by swapping with its neighbour as long as it's smaller. After each outer pass, everything to the left is sorted relative to each other.

## Approach

Store the current element as `value = nums[i+1]`. Then walk `j` backwards from `i+1` down to 0 — whenever `value` is less than `nums[j]`, swap `nums[j]` and `nums[j+1]` to move the element one step left. This bubbles the element into its correct sorted position.

## Code

```cpp
class Solution {
public:
    vector<int> sortArray(vector<int>& nums) {
        for (int i = 0; i < (int)nums.size() - 1; i++) {
            int value = nums[i + 1];
            // walk the element at i+1 backwards into its sorted position
            for (int j = i + 1; j >= 0; j--) {
                if (value < nums[j]) {
                    swap(nums[j], nums[j + 1]);
                }
            }
        }
        return nums;
    }
};
```

## Dry Run

Input: `[3, 1, 2]`

```
i=0, value=nums[1]=1:
  j=1: 1 < nums[1]=1? No
  j=0: 1 < nums[0]=3? Yes → swap(0,1) → [1, 3, 2]

i=1, value=nums[2]=2:
  j=2: 2 < nums[2]=2? No
  j=1: 2 < nums[1]=3? Yes → swap(1,2) → [1, 2, 3]
  j=0: 2 < nums[0]=1? No

Result: [1, 2, 3] ✓
```

## Complexity

**Time:** O(n²) — each element may have to walk all the way to index 0 in the worst case (reverse-sorted input).

**Space:** O(1) — sorts in-place, only a `value` variable and loop indices.

## Key Pattern

Insertion sort is "pull one element out and walk it left until it fits." The swap-based version is equivalent to the classic key-shift version — just trades element shifting for adjacent swaps. Both are O(n²) worst case but O(n) on nearly-sorted data, which makes insertion sort the go-to choice for small or almost-sorted arrays.
