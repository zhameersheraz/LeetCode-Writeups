# 345. Reverse Vowels of a String

Writeup by: zham

- **Difficulty:** Easy
- **Topic:** Two Pointers, String
- **Platform:** LeetCode
- **Language:** C++

---

## Description

> Given a string `s`, reverse only all the vowels in the string and return it.
>
> The vowels are `'a'`, `'e'`, `'i'`, `'o'`, and `'u'`, and they can appear in both lower and upper cases, more than once.
>
> **Example 1:**
>
> ```
> Input: s = "IceCreAm"
> Output: "AceCreIm"
> Explanation: The vowels in s are ['I', 'e', 'e', 'A']. On reversing the vowels, s becomes "AceCreIm".
> ```
>
> **Example 2:**
>
> ```
> Input: s = "leetcode"
> Output: "leotcede"
> ```
>
> **Constraints:**
>
> - `1 <= s.length <= 3 * 10^5`
> - `s` consist of printable ASCII characters.

---

## How I thought about it

This is the same shape as the "reverse the whole string" problem, except I only swap on certain characters. So the natural approach is still two pointers, but the pointers have to skip past any non-vowels.

The trick is the inner `while` loops. Each one walks its pointer past non-vowel characters until it lands on a vowel or until the two pointers meet. The `i < j` check in the inner loops is important. Without it, a pointer could run past the other one and we'd swap a vowel with something already swapped.

Once both pointers are on vowels, swap them, then step both inward. Repeat.

Both upper and lower case vowels count. The string `"aeiouAEIOU"` keeps them all in one place to check against.

---

## Solution

```cpp
class Solution {
public:
    string reverseVowels(string s) {
        string vowels = "aeiouAEIOU";
        int i = 0, j = s.length() - 1;
        while (i < j) {
            while (i < j && vowels.find(s[i]) == string::npos) i++;
            while (i < j && vowels.find(s[j]) == string::npos) j--;
            if (i < j) {
                swap(s[i], s[j]);
                i++;
                j--;
            }
        }
        return s;
    }
};
```

`#include <algorithm>` is needed for `swap`.

---

## Walking through an example

`s = "IceCreAm"`, vowels = `a e i o u A E I O U`:

- i=0 ('I'), j=6 ('m'): 'I' is a vowel, 'm' is not. Move j back. j=5 ('A'): 'A' is a vowel. Swap. s becomes `"AceCreIm"`. i=1, j=4.
- i=1 ('c'), j=4 ('e'): 'c' is not. Move i forward. i=2 ('e'): 'e' is a vowel. Swap. s still `"AceCreIm"`. i=3, j=3.
- i=3, j=3, loop ends.

Result: `"AceCreIm"`.

---

## Cost

- **Time:** O(n), each character is visited at most once by each pointer.
- **Space:** O(1) for the vowels lookup string. The result is modified in place.

---

## What I took away from this one

The shape of this problem is two pointers walking toward each other and swapping. I have seen it now in 1768 and here. The pattern of "two pointers that skip past certain characters" is a small extension of the same idea.

The `i < j` guard inside the inner while loops was the bit I had to think about. Without it, if a pointer runs into the other one without finding a vowel, it would still loop. The guard makes sure each pointer stops as soon as it meets the other one.

The other small thing: using `string::npos` as the "not found" return from `find`. That is the standard pattern in C++ for "is this character in this string", and it reads cleaner than writing a separate `isVowel` function for this short list.

