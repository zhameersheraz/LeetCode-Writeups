# 238. Product of Array Except Self

Writeup by: zham

- **Difficulty:** Medium
- **Topic:** Array, Prefix Product
- **Platform:** LeetCode
- **Language:** Java

---

## Description

> Given an integer array `nums`, return an array `answer` such that `answer[i]` is equal to the product of all the elements of `nums` except `nums[i]`.
>
> The product of any prefix or suffix of `nums` is **guaranteed** to fit in a 32-bit integer.
>
> You must write an algorithm that runs in `O(n)` time and **without using the division operation**.
>
> **Example 1:**
>
> ```
> Input: nums = [1,2,3,4]
> Output: [24,12,8,6]
> ```
>
> **Example 2:**
>
> ```
> Input: nums = [-1,1,0,-3,3]
> Output: [0,0,9,0,0]
> ```
>
> **Constraints:**
>
> - `2 <= nums.length <= 10^5`
> - `-30 <= nums[i] <= 30`
> - The input is generated such that `answer[i]` is **guaranteed** to fit in a 32-bit integer.
>
> **Follow up:** Can you solve the problem in `O(1)` extra space complexity? (The output array **does not** count as extra space for space complexity analysis.)

---

## How I thought about it

The naive approach is to multiply everything for each position, but that is O(n squared). Division is banned too, so I cannot just compute the full product and divide.

The key observation: for any position `i`, the answer is the product of two things:
- Everything to the left of `i`
- Everything to the right of `i`

If I have both, I just multiply them. So the problem becomes "compute prefix products" and "compute suffix products".

I can build the prefix products in one pass and store them in the answer array. Then I walk from the right, keeping a running suffix product, and multiply it into each answer slot as I go. That way I never need a second array. The answer array becomes the prefix products at first, then becomes the final answer after the right-to-left pass.

Two passes total. O(n) time. O(1) extra space (only the running suffix variable).

---

## Solution

```java
class Solution {
    public int[] productExceptSelf(int[] nums) {
        int n = nums.length;
        int[] answer = new int[n];
        answer[0] = 1;
        for (int i = 1; i < n; i++) {
            answer[i] = answer[i-1] * nums[i-1];
        }
        int suffix = 1;
        for (int i = n - 1; i >= 0; i--) {
            answer[i] = answer[i] * suffix;
            suffix = suffix * nums[i];
        }
        return answer;
    }
}
```

---

## Walking through an example

`nums = [1, 2, 3, 4]`:

**First pass (left to right, building prefix products):**

| i | nums[i-1] | answer[i-1] (so far) | answer[i] |
|---|-----------|----------------------|-----------|
| 0 | -         | -                    | 1 (set directly) |
| 1 | 1         | 1                    | 1 × 1 = 1 |
| 2 | 2         | 1                    | 1 × 2 = 2 |
| 3 | 3         | 2                    | 2 × 3 = 6 |

After this pass, `answer = [1, 1, 2, 6]`.

**Second pass (right to left, multiplying by suffix):**

| i | answer[i] before | suffix | answer[i] after | new suffix |
|---|------------------|--------|------------------|------------|
| 3 | 6                | 1      | 6 × 1 = 6        | 1 × 4 = 4 |
| 2 | 2                | 4      | 2 × 4 = 8        | 4 × 3 = 12 |
| 1 | 1                | 12     | 1 × 12 = 12      | 12 × 2 = 24 |
| 0 | 1                | 24     | 1 × 24 = 24      | 24 × 1 = 24 |

Final `answer = [24, 12, 8, 6]`. Matches the expected output.

---

## Cost

- **Time:** O(n), two linear passes.
- **Space:** O(1) extra, only one running `suffix` variable. The `answer` array counts as output, not extra space.

---

## What I took away from this one

The "no division" constraint is what makes this problem interesting. With division I could do it in one pass. Without it, I have to think about it as two halves: left and right.

The trick of using the output array as a temporary workspace is one I want to remember. By the end of the second pass, every slot has been written twice, but I never needed a second array. The "output does not count as extra space" rule in the follow-up is the hint that this trick is the right approach.

The same shape showed up in 238 as it does in problems like "running sum" or "cumulative product". Walk one way, then walk back, multiply or add as you go. The two-pass pattern is small but useful, and now I have it memorized.
