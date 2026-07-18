# 1071. Greatest Common Divisor of Strings

Writeup by: zham

- **Difficulty:** Easy
- **Topic:** Math, String
- **Platform:** LeetCode
- **Language:** C++

---

## Description

> For two strings `s` and `t`, we say "`t` divides `s`" if and only if `s = t + t + t + ... + t + t` (i.e., `t` is concatenated with itself one or more times).
>
> Given two strings `str1` and `str2`, return the largest string `x` such that `x` divides both `str1` and `str2`.
>
> **Example 1:**
>
> ```
> Input: str1 = "ABCABC", str2 = "ABC"
> Output: "ABC"
> ```
>
> **Example 2:**
>
> ```
> Input: str1 = "ABABAB", str2 = "ABAB"
> Output: "AB"
> ```
>
> **Example 3:**
>
> ```
> Input: str1 = "LEET", str2 = "CODE"
> Output: ""
> ```
>
> **Example 4:**
>
> ```
> Input: str1 = "AAAAAB", str2 = "AAA"
> Output: ""
> ```
>
> **Constraints:**
>
> - `1 <= str1.length, str2.length <= 1000`
> - `str1` and `str2` consist of English uppercase letters.

---

## How I thought about it

At first I was about to start pulling substrings of `str1` and checking if they divide both. That would be slow and ugly.

Then I remembered a key property. If a string `x` divides both `str1` and `str2`, then `str1 + str2` and `str2 + str1` have to come out equal. They are both just the same repeating pattern of `x` written out a different number of times.

So step one is just check that. If `str1 + str2 != str2 + str1`, return empty string. No common divisor is possible.

If they match, the answer has to be made of `x` repeated, and the length of the longest `x` is the GCD of the two lengths. So step two is just `gcd(len(str1), len(str2))`, then take the first that many characters of either string.

C++ has `std::gcd` in `<numeric>`, so the math part is one line.

---

## Solution

```cpp
class Solution {
public:
    string gcdOfStrings(string str1, string str2) {
        if (str1 + str2 != str2 + str1) return "";
        int g = gcd((int)str1.length(), (int)str2.length());
        return str1.substr(0, g);
    }
};
```

`#include <numeric>` is needed at the top for `gcd`.

---

## Walking through an example

`str1 = "ABABAB"`, `str2 = "ABAB"`:

1. `str1 + str2` is `"ABABABABAB"`.
2. `str2 + str1` is `"ABABABABAB"`.
3. They match, so a common divisor exists.
4. `gcd(6, 4) = 2`.
5. `str1.substr(0, 2)` is `"AB"`.
6. Return `"AB"`.

`str1 = "LEET"`, `str2 = "CODE"`:

1. `str1 + str2` is `"LEETCODE"`.
2. `str2 + str1` is `"CODELEET"`.
3. They don't match, so no common divisor.
4. Return `""`.

---

## Cost

- **Time:** O(n + m), where n and m are the lengths of `str1` and `str2`. The two concatenations each take O(n + m) and the comparison takes O(n + m). The GCD and substr are tiny.
- **Space:** O(n + m) for the two concatenated strings.

---

## What I took away from this one

The big idea is the `str1 + str2 == str2 + str1` check. That one line replaces a whole loop trying every possible substring.

The second idea is realizing that once you know a common divisor exists, its length is the GCD of the two string lengths. That works because if `x` divides `str1` and `str2`, the lengths of `str1` and `str2` are both multiples of `len(x)`, so the longest possible `len(x)` is their GCD.

Euclid's GCD algorithm from math class actually shows up in code, and it does the work in one line with `std::gcd`. Two ideas from very different places (number theory and string patterns) coming together in one short function felt good.
