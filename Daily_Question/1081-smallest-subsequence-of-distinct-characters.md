# 1081. Smallest Subsequence of Distinct Characters

**Category:** Daily Question
**Difficulty:** Medium
**Platform:** LeetCode

Writeup by: zham

## Description

> Given a string `s`, return the *lexicographically smallest subsequence* of `s` that contains all the distinct characters of `s` exactly once.

## How I thought about it

The phrase "subsequence containing all distinct characters exactly once" tells me I am picking one copy of each unique letter while keeping the original order. The "lexicographically smallest" part is the real push. It means at every position in the result, I want the smallest character possible.

I went greedy: walk through `s` from left to right, building the result one character at a time. At each character, I have a choice: keep it, or skip it. The greedy rule: if a smaller character comes along later, and the bigger character I have at the top of my result still appears later in the string, I can safely pop the bigger one and put the smaller one in its place. If the bigger character does not appear later, I have to keep it, no choice.

That is a monotonic stack with last-occurrence tracking. The stack grows, and the "monotonic" part means once a character is at the top, anything below it in the stack is smaller. The lastIdx array tells me, for each letter, where its final appearance is, so I know if I can throw away a character and grab it again later.

Same problem as LeetCode 316 (Remove Duplicate Letters). They share the description and the answer.

## Solution

```java
class Solution {
    public String smallestSubsequence(String s) {
        int[] lastIdx = new int[26];
        for (int i = 0; i < s.length(); i++) {
            lastIdx[s.charAt(i) - 'a'] = i;
        }
        
        boolean[] inStack = new boolean[26];
        StringBuilder stack = new StringBuilder();
        
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            int idx = c - 'a';
            
            if (inStack[idx]) continue;
            
            while (stack.length() > 0 && stack.charAt(stack.length() - 1) > c 
                   && lastIdx[stack.charAt(stack.length() - 1) - 'a'] > i) {
                char top = stack.charAt(stack.length() - 1);
                inStack[top - 'a'] = false;
                stack.deleteCharAt(stack.length() - 1);
            }
            
            stack.append(c);
            inStack[idx] = true;
        }
        
        return stack.toString();
    }
}
```

## Walking through an example

Input: `s = "cbacdcbc"`

Pre-compute lastIdx (last index of each character):
- a -> 2
- b -> 6
- c -> 7
- d -> 4

| i | char | lastIdx[c] | inStack? | Action | stack | inStack set |
|---|------|------------|----------|--------|-------|-------------|
| 0 | c    | 7          | no       | push c | "c"   | {c}         |
| 1 | b    | 6          | no       | c > b, lastIdx[c]=7 > 1, pop c. Push b | "b" | {b} |
| 2 | a    | 2          | no       | b > a, lastIdx[b]=6 > 2, pop b. Push a | "a" | {a} |
| 3 | c    | 7          | no       | a < c, no pop. Push c | "ac"  | {a, c}      |
| 4 | d    | 4          | no       | c < d, no pop. Push d | "acd" | {a, c, d}   |
| 5 | c    | 7          | yes      | skip   | "acd" | {a, c, d}   |
| 6 | b    | 6          | no       | d > b, lastIdx[d]=4 > 6? No. Stop. Push b | "acdb" | {a, c, d, b} |
| 7 | c    | 7          | yes      | skip   | "acdb" | {a, c, d, b} |

Output: `"acdb"`

## Cost

- **Time:** O(n), where n is the length of s. Each character is pushed at most once and popped at most once, so the inner while loop runs in amortized O(1) per character.
- **Space:** O(1) for the lastIdx and inStack arrays (fixed 26 letters), plus O(n) for the stack in the worst case.

## What I took away

The pattern here is a generic one I want to remember: "build a sequence character by character, and at each step pop the last character if it is bigger than the new one AND that popped character appears again later." That "appears again later" check is the part that requires the lastIdx array, and it is what turns a naive stack into a correct greedy solution. I had the right shape the first time but the comparison `lastIdx[top] > i` (current index) instead of `lastIdx[top] > 0` is what made it work. The decrement-on-use version of lastIdx fails because it loses the global last position once you have started using a character. Same skeleton, same problem on LeetCode 316 (Remove Duplicate Letters), worth knowing both links.
