# 151. Reverse Words in a String

Writeup by: zham

- **Difficulty:** Medium
- **Topic:** String
- **Platform:** LeetCode
- **Language:** Java

---

## Description

> Given an input string `s`, reverse the order of the **words**.
>
> A **word** is defined as a sequence of non-space characters. The words in `s` will be separated by at least one space.
>
> Return a string of the words in reverse order concatenated by a single space.
>
> Note that `s` may contain leading or trailing spaces or multiple spaces between two words. The returned string should only have a single space separating the words. Do not include any extra spaces.
>
> **Example 1:**
>
> ```
> Input: s = "the sky is blue"
> Output: "blue is sky the"
> ```
>
> **Example 2:**
>
> ```
> Input: s = "  hello world  "
> Output: "world hello"
> Explanation: Your reversed string should not contain leading or trailing spaces.
> ```
>
> **Example 3:**
>
> ```
> Input: s = "a good   example"
> Output: "example good a"
> Explanation: You need to reduce multiple spaces between two words to a single space in the reversed string.
> ```
>
> **Constraints:**
>
> - `1 <= s.length <= 10^4`
> - `s` contains English letters (upper-case and lower-case), digits, and spaces `' '`.
> - There is at least one word in `s`.

---

## How I thought about it

Three things have to be handled at once: the actual reversal, leading and trailing spaces, and multiple spaces between words. The good news is one line handles all of them.

`s.trim().split("\\s+")` does the heavy lifting:
- `trim()` strips spaces at both ends
- `split("\\s+")` splits on one or more whitespace characters, so a run of multiple spaces becomes one split point

After that, I have a clean array of words in original order. I just walk the array backward, appending each word plus a space. Skip the space on the last word so I do not leave a trailing one.

The `StringBuilder` is important. Concatenating strings in a loop in Java creates a new String object every time, which is slow. `StringBuilder` builds the result in place and dumps it at the end with `toString()`.

---

## Solution

```java
class Solution {
    public String reverseWords(String s) {
        String[] words = s.trim().split("\\s+");
        StringBuilder sb = new StringBuilder();
        for (int i = words.length - 1; i >= 0; i--) {
            sb.append(words[i]);
            if (i > 0) sb.append(" ");
        }
        return sb.toString();
    }
}
```

---

## Walking through an example

`s = "a good   example"`:

1. `trim()` keeps it as `"a good   example"` (no leading or trailing space to strip).
2. `split("\\s+")` gives `["a", "good", "example"]`. The three spaces between `good` and `example` collapse into one split point.
3. Loop backward:
   - `i = 2`: append `"example"`, then append a space
   - `i = 1`: append `"good"`, then append a space
   - `i = 0`: append `"a"`. No space (since `i` is not greater than 0)
4. Final string: `"example good a"`.

---

## Cost

- **Time:** O(n) where n is the length of `s`. The split scans the string once, the loop appends each word once.
- **Space:** O(n) for the words array and the result string.

---

## What I took away from this one

The `\\s+` regex in `split` is the workhorse. Without the `+` quantifier, `split(" ")` would treat each space as a separator and leave empty strings in the array when there are runs of spaces. The `+` says "one or more whitespace characters", which handles single spaces, multiple spaces, and tabs all the same way.

I also learned the right Java pattern for building a string in a loop. I almost wrote `result = result + word`, then remembered the warning from tutorials about that creating a new String every time. The fix is `StringBuilder` plus `toString()` at the end. It is a small thing but it shows up in every "build a string from pieces" problem on LeetCode.

The follow-up hint at the bottom of the problem says the same thing can be done in O(1) extra space if you reverse the string in place, then reverse each word. That is the "real" interview version of this problem. I will revisit that version when I am more comfortable with the in-place stuff.
