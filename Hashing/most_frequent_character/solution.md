# Solution — Frequency Counting

## Approach

This is a straightforward frequency counting problem.

**Step 1: Count frequencies**

Use an `unordered_map<char, int>` to count how many times each character appears.

```
"hello"  →  h:1, e:1, l:2, o:1
```

**Step 2: Find the max by iterating the string (not the map)**

Iterate through `s` a second time. For each character, check if its frequency beats the current max.

The critical detail: use `>` not `>=`.

```cpp
if (freq[c] > maxFreq)   // strict greater-than
```

This ensures that when there's a tie, the character that appears **first** in the string wins — because later characters with the same frequency won't overwrite the answer.

**Why iterate the string again instead of the map?**

`unordered_map` has no guaranteed iteration order. If you loop over the map to find the max, you can't control which character wins the tie. Iterating the original string preserves the order characters were encountered.

---

## Code

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    char mostFrequentCharacter(string s) {
        unordered_map<char, int> freq;
        for (char c : s)
            freq[c]++;

        char answer = s[0];
        int maxFreq = 0;

        for (char c : s) {
            if (freq[c] > maxFreq) {
                maxFreq = freq[c];
                answer = c;
            }
        }

        return answer;
    }
};
```

---

## Complexity

| | Complexity | Reason |
|--|------------|--------|
| Time | O(n) | Two passes over the string |
| Space | O(1) | Map holds at most 26 characters |

---

## Key Pattern

Whenever you see:
- "How many times does each element occur?"
- "Which element occurs the most / least?"
- "Which element occurs exactly once?"
- "Are two strings anagrams?"

→ **Frequency map** is the tool. Build it in one pass, answer the question in one more pass.
