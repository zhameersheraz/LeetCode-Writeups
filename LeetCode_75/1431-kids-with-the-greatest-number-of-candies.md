# 1431. Kids With the Greatest Number of Candies

Writeup by: zham

- **Difficulty:** Easy
- **Topic:** Array
- **Platform:** LeetCode
- **Language:** C++

---

## Description

> There are `n` kids with candies. You are given an integer array `candies`, where each `candies[i]` represents the number of candies the `i`th kid has, and an integer `extraCandies`, denoting the number of extra candies that you have.
>
> Return a boolean array `result` of length `n`, where `result[i]` is `true` if, after giving the `i`th kid all the `extraCandies`, they will have the **greatest** number of candies among all the kids, or `false` otherwise.
>
> Note that **multiple** kids can have the greatest number of candies.
>
> **Example 1:**
>
> ```
> Input: candies = [2,3,5,1,3], extraCandies = 3
> Output: [true,true,true,false,true]
> Explanation: If you give all extraCandies to:
> - Kid 1, they will have 2 + 3 = 5 candies, which is the greatest among the kids.
> - Kid 2, they will have 3 + 3 = 6 candies, which is the greatest among the kids.
> - Kid 3, they will have 5 + 3 = 8 candies, which is the greatest among the kids.
> - Kid 4, they will have 1 + 3 = 4 candies, which is not the greatest among the kids.
> - Kid 5, they will have 3 + 3 = 6 candies, which is the greatest among the kids.
> ```
>
> **Example 2:**
>
> ```
> Input: candies = [4,2,1,1,2], extraCandies = 1
> Output: [true,false,false,false,false]
> Explanation: There is only 1 extra candy. Kid 1 will always have the greatest number of candies, even if a different kid is given the extra candy.
> ```
>
> **Example 3:**
>
> ```
> Input: candies = [12,1,12], extraCandies = 10
> Output: [true,false,true]
> ```
>
> **Constraints:**
>
> - `n == candies.length`
> - `2 <= n <= 100`
> - `1 <= candies[i] <= 100`
> - `1 <= extraCandies <= 50`

---

## How I thought about it

The question is basically asking: for each kid, if I dump all the extra candies on them, can they tie or beat the current top kid?

So the whole thing is two steps:

1. Find the current highest count in the array. That is the bar.
2. For each kid, add `extraCandies` to their count and check if it reaches the bar. If yes, push `true`. If no, push `false`.

That's it. No fancy data structure, no extra pass. One pass to find max, one pass to build the answer.

---

## Solution

```cpp
class Solution {
public:
    vector<bool> kidsWithCandies(vector<int>& candies, int extraCandies) {
        int maxC = *max_element(candies.begin(), candies.end());
        vector<bool> result;
        for (int c : candies) {
            result.push_back(c + extraCandies >= maxC);
        }
        return result;
    }
};
```

`#include <algorithm>` is needed for `max_element`.

---

## Walking through an example

`candies = [2,3,5,1,3]`, `extraCandies = 3`:

1. `maxC = 5` (the biggest original count).
2. For each kid, check if `count + 3 >= 5`:
   - Kid 1: `2 + 3 = 5`, `5 >= 5` is `true`
   - Kid 2: `3 + 3 = 6`, `6 >= 5` is `true`
   - Kid 3: `5 + 3 = 8`, `8 >= 5` is `true`
   - Kid 4: `1 + 3 = 4`, `4 >= 5` is `false`
   - Kid 5: `3 + 3 = 6`, `6 >= 5` is `true`
3. Result: `[true, true, true, false, true]`.

---

## Cost

- **Time:** O(n), two linear passes over the array.
- **Space:** O(n) for the result vector.

---

## What I took away from this one

The phrase "greatest number of candies" was the part that clicked. It says greatest, not strictly greater. So the kid with the current top count doesn't have to beat themselves, they just need to tie. That is why the check is `>=` and not `>`.

Also, the problem has a small sneaky bit. "Multiple kids can have the greatest number" is easy to miss. If I had used `>` instead of `>=`, kid 1 in example 1 (who already had 2 candies) would not be able to reach 5 if all extras went elsewhere. But the answer says they can. The point is: even if you give all extras to kid 1, they end up at exactly 5, which ties the current max. So `>=` is right.

One of those problems where reading the problem twice is the whole solution.
