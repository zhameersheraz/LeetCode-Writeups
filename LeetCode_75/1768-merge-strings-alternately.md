# 1768. Merge Strings Alternately

Writeup by: zham

- **Difficulty:** Easy
- **Topic:** Two Pointers, String
- **Platform:** LeetCode
- **Language:** C++

---

## Description

> You are given two strings `word1` and `word2`. Merge the strings by adding letters in alternating order, starting with `word1`. If a string is longer than the other, append the additional letters onto the end of the merged string.
>
> Return the merged string.
>
> **Example 1:**
>
> ```
> Input: word1 = "abc", word2 = "pqr"
> Output: "apbqcr"
> Explanation: The merged string will be merged as so:
> word1:  a   b   c
> word2:    p   q   r
> merged: a p b q c r
> ```
>
> **Example 2:**
>
> ```
> Input: word1 = "ab", word2 = "pqrs"
> Output: "apbqrs"
> Explanation: Notice that as word2 is longer, "rs" is appended to the end.
> word1:  a   b
> word2:    p   q   r   s
> merged: a p b q   r   s
> ```
>
> **Example 3:**
>
> ```
> Input: word1 = "abcd", word2 = "pq"
> Output: "apbqcd"
> Explanation: Notice that as word1 is longer, "cd" is appended to the end.
> word1:  a   b   c   d
> word2:    p   q
> merged: a p b q c   d
> ```
>
> **Constraints:**
>
> - `1 <= word1.length, word2.length <= 100`
> - `word1` and `word2` consist of lowercase English letters.

---

## How I thought about it

When I see two sequences and I need to walk through both at the same time, my brain goes to two pointers. That's the move here too.

One pointer for `word1`, one for `word2`. Step through both, take a character from each on every turn. The catch is the strings might be different lengths, so the loop can't just stop when one runs out, it has to keep going until both are empty.

That's where the `||` (OR) in the while loop comes in. As long as at least one of the pointers still has work to do, keep going. The `if` checks inside the loop skip the empty string cleanly.

---

## Solution

```cpp
class Solution {
public:
    string mergeAlternately(string word1, string word2) {
        string result = "";
        int i = 0, j = 0;
        while (i < word1.length() || j < word2.length()) {
            if (i < word1.length()) result += word1[i++];
            if (j < word2.length()) result += word2[j++];
        }
        return result;
    }
};
```

---

## Walking through an example

`word1 = "ab"`, `word2 = "pqrs"`:

| i | j | result so far | what we did |
|---|---|---------------|-------------|
| 0 | 0 | `""`         | take `word1[0]` = `'a'`, i moves to 1 |
| 1 | 0 | `"a"`        | take `word2[0]` = `'p'`, j moves to 1 |
| 1 | 1 | `"ap"`       | take `word1[1]` = `'b'`, i moves to 2 |
| 2 | 1 | `"apb"`      | word1 is done, skip it. Take `word2[1]` = `'q'`, j moves to 2 |
| 2 | 2 | `"apbq"`     | word1 still done. Take `word2[2]` = `'r'`, j moves to 3 |
| 2 | 3 | `"apbqr"`    | Take `word2[3]` = `'s'`, j moves to 4 |
| 2 | 4 | `"apbqrs"`   | both pointers done, return |

---

## Cost

- **Time:** O(n), where n is the total length of both strings. We touch each character once.
- **Space:** O(n) for the result string.

---

## What I took away from this one

The thing that made this click for me was the `||` in the while loop. If I had used `&&` (AND), the loop would stop the moment one string ended, and I'd have to write extra code to handle the leftover. With `||`, the leftover just falls out naturally through the `if` checks.

Also, the `i++` inside `word1[i++]` is post-increment, so it grabs `word1[i]` first, then bumps i. That lets me write the take-and-move in one line.

Two pointers showed up here, and it'll keep showing up in string and array problems. Pattern worth remembering.
