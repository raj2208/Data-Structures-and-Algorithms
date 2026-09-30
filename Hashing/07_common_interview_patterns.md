# Common Hashing Patterns in Google Interviews (C++)

## How to Think About Hashing in an Interview

When you see a problem, ask:
1. Do I need O(1) lookup? → `unordered_map` / `unordered_set`
2. Do I need to count occurrences? → `unordered_map<T, int>` with `operator[]`
3. Do I need to detect a duplicate? → `unordered_set`
4. Do I need to group items by some property? → `unordered_map<K, vector<T>>`
5. Do I need to remember something from earlier in an array? → `unordered_map` as "memory"
6. Do I need to find a complement (two numbers summing to X)? → `unordered_map` for lookups
7. Do I need sorted keys or range queries? → `map` / `set`

The single most powerful hashing pattern: **transform a problem into "have I seen this value before?"**

---

## Pattern 1: Frequency Counting

**Core template**:
```cpp
std::unordered_map<T, int> freq;
for (auto& item : collection)
    freq[item]++;  // operator[] default-initializes int to 0
```

**When to use**: Anagram detection, character counting, majority element, most frequent element.

### Example: Valid Anagram
```cpp
bool isAnagram(std::string s, std::string t) {
    if (s.size() != t.size()) return false;
    std::unordered_map<char, int> freq;
    for (char c : s) freq[c]++;
    for (char c : t) {
        if (--freq[c] < 0) return false;
    }
    return true;
    // O(n) time, O(1) space (26 letters max)
}
```

### Example: Group Anagrams
```cpp
std::vector<std::vector<std::string>> groupAnagrams(std::vector<std::string>& strs) {
    std::unordered_map<std::string, std::vector<std::string>> groups;
    for (auto& word : strs) {
        std::string key = word;
        std::sort(key.begin(), key.end());  // canonical form: "eat" -> "aet"
        groups[key].push_back(word);
    }
    std::vector<std::vector<std::string>> result;
    for (auto& [key, group] : groups)
        result.push_back(group);
    return result;
}
```

Alternative key without sorting (O(26) per word instead of O(k log k)):
```cpp
std::string key(26, 0);
for (char c : word) key[c - 'a']++;
```

---

## Pattern 2: Two-Sum / Complement Lookup

**Core idea**: Instead of scanning all pairs O(n²), store what you've seen and look up the complement.

```cpp
std::vector<int> twoSum(std::vector<int>& nums, int target) {
    std::unordered_map<int, int> seen;  // value → index
    for (int i = 0; i < (int)nums.size(); ++i) {
        int complement = target - nums[i];
        auto it = seen.find(complement);
        if (it != seen.end())
            return {it->second, i};
        seen[nums[i]] = i;
    }
    return {};
}
```

**Generalizations**:
- **Three Sum**: Fix one element with a for loop, reduce inner part to Two Sum → O(n²)
- **Subarray Sum = K**: Use prefix sums + hash map (see Pattern 4)
- **Pair with given difference**: Look up `num + k` in seen set

---

## Pattern 3: Sliding Window + Hash Map

When you need to track a window's properties (character counts, presence) as it slides:

### Example: Longest Substring Without Repeating Characters
```cpp
int lengthOfLongestSubstring(std::string s) {
    std::unordered_map<char, int> last_seen;  // char → last index seen
    int max_len = 0, left = 0;

    for (int right = 0; right < (int)s.size(); ++right) {
        char c = s[right];
        auto it = last_seen.find(c);
        if (it != last_seen.end() && it->second >= left)
            left = it->second + 1;   // shrink window: move left past duplicate
        last_seen[c] = right;
        max_len = std::max(max_len, right - left + 1);
    }
    return max_len;
}
```

### Example: Minimum Window Substring
```cpp
std::string minWindow(std::string s, std::string t) {
    std::unordered_map<char, int> need;
    for (char c : t) need[c]++;

    int missing = t.size(), left = 0;
    int best_start = 0, best_len = INT_MAX;

    for (int right = 0; right < (int)s.size(); ++right) {
        if (need[s[right]]-- > 0) missing--;  // using a needed char

        while (missing == 0) {  // window is valid — try to shrink
            if (right - left + 1 < best_len) {
                best_len = right - left + 1;
                best_start = left;
            }
            if (++need[s[left]] > 0) missing++;  // losing a needed char
            left++;
        }
    }
    return best_len == INT_MAX ? "" : s.substr(best_start, best_len);
}
```

---

## Pattern 4: Prefix Sum + Hash Map

For problems asking "how many subarrays satisfy condition X?". **Memorize this template.**

```
prefix_sum[i] - prefix_sum[j] = k
  --> prefix_sum[j] = prefix_sum[i] - k
  --> look up (prefix_sum[i] - k) in seen map
```

### Subarray Sum Equals K
```cpp
int subarraySum(std::vector<int>& nums, int k) {
    int count = 0, prefix = 0;
    std::unordered_map<int, int> seen;
    seen[0] = 1;  // empty prefix has sum 0

    for (int num : nums) {
        prefix += num;
        auto it = seen.find(prefix - k);
        if (it != seen.end()) count += it->second;
        seen[prefix]++;
    }
    return count;
}
```

### Count of Subarrays with Sum Divisible by K
```cpp
int subarraysDivByK(std::vector<int>& nums, int k) {
    int count = 0, prefix = 0;
    std::unordered_map<int, int> remainders;
    remainders[0] = 1;

    for (int num : nums) {
        prefix = ((prefix + num) % k + k) % k;  // handle negatives
        count += remainders[prefix];
        remainders[prefix]++;
    }
    return count;
}
```

**Key insight**: If `prefix[i] % k == prefix[j] % k`, then `sum(i+1..j)` is divisible by k.

### Longest Subarray with Equal 0s and 1s
```cpp
int longestSubarray(std::vector<int>& nums) {
    // Replace 0 with -1; find longest subarray with sum = 0
    std::unordered_map<int, int> first_seen;
    first_seen[0] = -1;
    int prefix = 0, max_len = 0;

    for (int i = 0; i < (int)nums.size(); ++i) {
        prefix += (nums[i] == 1) ? 1 : -1;
        auto it = first_seen.find(prefix);
        if (it != first_seen.end())
            max_len = std::max(max_len, i - it->second);
        else
            first_seen[prefix] = i;
    }
    return max_len;
}
```

---

## Pattern 5: Grouping / Canonicalization

Group items that are "equivalent" under some transformation. The canonical form is the key.

| Problem | Canonical Form |
|---------|----------------|
| Anagrams | sorted string |
| Isomorphic strings | relative-order signature |
| Shift equivalent strings | normalized to start at 'a' |
| Same tree structure | serialized form |

### Example: Isomorphic Strings
```cpp
bool isIsomorphic(std::string s, std::string t) {
    std::unordered_map<char,char> s_to_t, t_to_s;
    for (int i = 0; i < (int)s.size(); ++i) {
        char sc = s[i], tc = t[i];
        if (s_to_t.count(sc) && s_to_t[sc] != tc) return false;
        if (t_to_s.count(tc) && t_to_s[tc] != sc) return false;
        s_to_t[sc] = tc;
        t_to_s[tc] = sc;
    }
    return true;
}
```

### Example: Word Pattern
```cpp
bool wordPattern(std::string pattern, std::string s) {
    std::istringstream iss(s);
    std::vector<std::string> words(
        std::istream_iterator<std::string>{iss},
        std::istream_iterator<std::string>{}
    );
    if (pattern.size() != words.size()) return false;

    std::unordered_map<char, std::string> c2w;
    std::unordered_map<std::string, char> w2c;

    for (int i = 0; i < (int)pattern.size(); ++i) {
        char c = pattern[i];
        const std::string& w = words[i];
        if (c2w.count(c) && c2w[c] != w) return false;
        if (w2c.count(w) && w2c[w] != c) return false;
        c2w[c] = w;
        w2c[w] = c;
    }
    return true;
}
```

---

## Pattern 6: Cycle Detection via Hashing

When you can't modify the input, a hash set tracks visited states.

### Example: Happy Number
```cpp
bool isHappy(int n) {
    auto digit_sum_sq = [](int x) {
        int s = 0;
        while (x) { int d = x % 10; s += d*d; x /= 10; }
        return s;
    };
    std::unordered_set<int> seen;
    while (n != 1) {
        if (seen.count(n)) return false;
        seen.insert(n);
        n = digit_sum_sq(n);
    }
    return true;
}
```

### Example: Find Duplicate in Array
```cpp
int findDuplicate(std::vector<int>& nums) {
    std::unordered_set<int> seen;
    for (int n : nums) {
        if (seen.count(n)) return n;
        seen.insert(n);
    }
    return -1;  // unreachable
}
```

---

## Pattern 7: HashMap as Memoization

```cpp
#include <unordered_map>

std::unordered_map<int, long long> memo;

long long fib(int n) {
    if (n <= 1) return n;
    auto it = memo.find(n);
    if (it != memo.end()) return it->second;
    return memo[n] = fib(n-1) + fib(n-2);
}
```

For 2D memoization, encode the state as a single key:
```cpp
std::unordered_map<long long, int> dp;

// Encode (i, j) as a single long long
auto key = [](int i, int j) -> long long {
    return ((long long)i << 32) | (unsigned)j;
};

dp[key(i, j)] = value;
```

---

## Pattern 8: Two-Pass with Hash Map

### Example: First Non-Repeating Character
```cpp
int firstUniqueChar(std::string s) {
    std::unordered_map<char, int> freq;
    for (char c : s) freq[c]++;
    for (int i = 0; i < (int)s.size(); ++i)
        if (freq[s[i]] == 1) return i;
    return -1;
}
```

### Example: Majority Element
```cpp
int majorityElement(std::vector<int>& nums) {
    std::unordered_map<int, int> freq;
    int n = nums.size();
    for (int x : nums)
        if (++freq[x] > n / 2) return x;
    return -1;
}
```

---

## Pattern 9: Coordinate Compression

When values are large but the number of distinct values is small, compress them to indices:

```cpp
std::vector<int> compress(std::vector<int>& coords) {
    std::vector<int> sorted = coords;
    std::sort(sorted.begin(), sorted.end());
    sorted.erase(std::unique(sorted.begin(), sorted.end()), sorted.end());

    std::unordered_map<int, int> rank;
    for (int i = 0; i < (int)sorted.size(); ++i)
        rank[sorted[i]] = i;

    std::vector<int> result;
    for (int c : coords) result.push_back(rank[c]);
    return result;
}

// coords = {100, 500, 300, 100, 500}
// compressed = {0, 2, 1, 0, 2}
```

---

## Pattern 10: Fixed-Size Window with Frequency Map

Track character counts as a fixed window moves. Use a `matches` counter to avoid O(26) map comparison on each step.

### Example: Permutation in String
```cpp
bool checkInclusion(std::string s1, std::string s2) {
    if (s1.size() > s2.size()) return false;
    int k = s1.size();

    std::unordered_map<char, int> need, window;
    for (char c : s1) need[c]++;

    int matches = 0, total = need.size();

    for (int i = 0; i < (int)s2.size(); ++i) {
        char c = s2[i];
        window[c]++;
        if (need.count(c) && window[c] == need[c]) matches++;

        if (i >= k) {
            char left = s2[i - k];
            if (need.count(left) && window[left] == need[left]) matches--;
            if (--window[left] == 0) window.erase(left);
        }

        if (matches == total) return true;
    }
    return false;
}
```

---

## Summary Table

| Pattern | When to Recognize | Complexity |
|---------|-------------------|------------|
| Frequency map | "count occurrences", "most common" | O(n) |
| Two-sum / complement | "two elements summing to X" | O(n) |
| Prefix sum + map | "subarrays with property X" | O(n) |
| Sliding window + map | "substring/subarray of length k" | O(n) |
| Canonicalization | "group by equivalence" | O(n × key_cost) |
| Cycle detection | "loop in sequence" | O(n) |
| Memoization | "overlapping subproblems" | problem-specific |
| Coordinate compression | "indices are huge, values sparse" | O(n log n) |

---

## What's Next

- [08_consistent_hashing.md](08_consistent_hashing.md) — Distributed systems hashing (load balancing, sharding) — important for Google system design interviews
