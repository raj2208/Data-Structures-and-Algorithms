# Check if All Characters Have Equal Number of Occurrences

## Problem Statement

Given a string `s`, return `true` if `s` is a **good** string, `false` otherwise.

A string is **good** if every character that appears in it has the **same** frequency.

---

## Examples

**Example 1**
```
Input:  s = "abacbc"
Output: true

a→2  b→2  c→2
All characters appear the same number of times.
```

**Example 2**
```
Input:  s = "aaabb"
Output: false

a→3  b→2
Not equal — return false.
```

**Example 3 — Single character**
```
Input:  s = "aaa"
Output: true

Only one unique character, trivially good.
```

---

## Constraints

- `1 <= s.length <= 1000`
- `s` consists of lowercase English letters only

---

## Test Cases

| Input | Expected Output |
|-------|----------------|
| `"abacbc"` | `true` |
| `"aaabb"` | `false` |
| `"aaa"` | `true` |
| `"z"` | `true` |
| `"abcd"` | `true` |
| `"aab"` | `false` |
