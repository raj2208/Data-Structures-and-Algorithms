# GCD of Two Numbers

## Problem Statement

Given two positive integers `a` and `b`, find their **Greatest Common Divisor (GCD)**.

The GCD of two integers is the largest positive integer that divides both of them without leaving a remainder.

---

## Examples

**Example 1**
```
Input:  a = 48, b = 18
Output: 6

6 is the largest number that divides both 48 and 18.
```

**Example 2**
```
Input:  a = 100, b = 75
Output: 25
```

**Example 3 — One divides the other**
```
Input:  a = 12, b = 4
Output: 4
```

**Example 4 — Coprime numbers (GCD = 1)**
```
Input:  a = 7, b = 13
Output: 1

7 and 13 share no common factor other than 1.
```

---

## Constraints

- `1 <= a, b <= 10^9`

---

## Test Cases

| a | b | Expected GCD |
|---|---|---|
| 48 | 18 | 6 |
| 100 | 75 | 25 |
| 12 | 4 | 4 |
| 7 | 13 | 1 |
| 9 | 9 | 9 |
