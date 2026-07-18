# 1929. Q1 Concatenation of Array

Writeup by: zham

- **Difficulty:** Easy
- **Topic:** Array
- **Platform:** LeetCode (Quest - Array I)
- **Language:** Java

---

## Description

> Given an integer array `nums` of length `n`, you want to create an array `ans` of length `2n` where `ans[i] == nums[i]` and `ans[i + n] == nums[i]` for `0 <= i < n` (0-indexed).
>
> Specifically, `ans` is the **concatenation** of two `nums` arrays.
>
> Return the array `ans`.
>
> **Example 1:**
>
> ```
> Input: nums = [1,2,1]
> Output: [1,2,1,1,2,1]
> Explanation: The array ans is formed as follows:
> - ans = [nums[0],nums[1],nums[2],nums[0],nums[1],nums[2]]
> - ans = [1,2,1,1,2,1]
> ```
>
> **Example 2:**
>
> ```
> Input: nums = [1,3,2,1]
> Output: [1,3,2,1,1,3,2,1]
> Explanation: The array ans is formed as follows:
> - ans = [nums[0],nums[1],nums[2],nums[3],nums[0],nums[1],nums[2],nums[3]]
> - ans = [1,3,2,1,1,3,2,1]
> ```
>
> **Constraints:**
>
> - `n == nums.length`
> - `1 <= n <= 1000`
> - `1 <= nums[i] <= 1000`

---

## How I thought about it

The result is just `nums` written twice. So the work is: allocate an array of size `2n`, then walk through the original once, copying each element to two positions.

I can do it in one pass instead of two by writing to both spots on the same iteration. The first half of the result gets `nums[i]` written at index `i`, the second half gets `nums[i]` written at index `i + n`. Same loop, two writes per step.

---

## Solution

```java
class Solution {
    public int[] getConcatenation(int[] nums) {
        int n = nums.length;
        int[] ans = new int[2 * n];
        for (int i = 0; i < n; i++) {
            ans[i] = nums[i];
            ans[i + n] = nums[i];
        }
        return ans;
    }
}
```

---

## Walking through an example

`nums = [1, 3, 2, 1]`, so n = 4 and `ans` has length 8.

| i | nums[i] | ans[i]    | ans[i + n] |
|---|---------|-----------|------------|
| 0 | 1       | ans[0]=1  | ans[4]=1   |
| 1 | 3       | ans[1]=3  | ans[5]=3   |
| 2 | 2       | ans[2]=2  | ans[6]=2   |
| 3 | 1       | ans[3]=1  | ans[7]=1   |

Final `ans = [1, 3, 2, 1, 1, 3, 2, 1]`. Matches the expected output.

---

## Cost

- **Time:** O(n), one pass over the input.
- **Space:** O(n) for the result array, which the problem asks for.

---

## What I took away from this one

The trick was doing two writes per loop iteration. The naive version (fill the first half, then loop again to fill the second half) is also O(n) but uses two passes. The two-writes-in-one-loop version is the same time but more obvious once you see it.

The `i + n` index is what makes the "second copy" line up. For `i = 0`, write at index `n`. For `i = 1`, write at `n + 1`. And so on. That way, after the loop, indices `n` through `2n - 1` hold the same values as indices `0` through `n - 1`.

This is the kind of problem where the answer is basically the question. The hard part is reading carefully enough to know you need a `2n` sized result, not `n`.
