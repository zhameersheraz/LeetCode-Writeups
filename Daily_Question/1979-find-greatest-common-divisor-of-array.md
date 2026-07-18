# 1979. Find Greatest Common Divisor of Array

Writeup by: zham

- **Difficulty:** Easy
- **Topic:** Array, Math
- **Platform:** LeetCode (Daily Question)
- **Language:** Java

---

## Description

> Given an integer array `nums`, return the **greatest common divisor** of the smallest number and largest number in `nums`.
>
> The **greatest common divisor** of two numbers is the largest positive integer that evenly divides both numbers.
>
> **Example 1:**
>
> ```
> Input: nums = [2,5,6,9,10]
> Output: 2
> Explanation:
> The smallest number in nums is 2.
> The largest number in nums is 10.
> The greatest common divisor of 2 and 10 is 2.
> ```
>
> **Example 2:**
>
> ```
> Input: nums = [7,5,6,8,3]
> Output: 1
> Explanation:
> The smallest number in nums is 3.
> The largest number in nums is 8.
> The greatest common divisor of 3 and 8 is 1.
> ```
>
> **Example 3:**
>
> ```
> Input: nums = [3,3]
> Output: 3
> Explanation:
> The smallest number in nums is 3.
> The largest number in nums is 3.
> The greatest common divisor of 3 and 3 is 3.
> ```
>
> **Constraints:**
>
> - `2 <= nums.length <= 1000`
> - `1 <= nums[i] <= 1000`

---

## How I thought about it

Two steps, both of which I have done before in earlier problems. Find the smallest and largest in the array, then compute the GCD of those two numbers. Done.

The min and max search is one pass through the array. I keep two variables, update them as I see new values.

The GCD is the Euclidean algorithm. It runs by replacing the larger number with the remainder of dividing it by the smaller one, repeatedly, until one of them is 0. The other one is the GCD.

I wrote the GCD as its own helper method since it has a loop of its own. Pulling it out keeps `findGCD` short and readable.

---

## Solution

```java
class Solution {
    public int findGCD(int[] nums) {
        int min = nums[0], max = nums[0];
        for (int n : nums) {
            if (n < min) min = n;
            if (n > max) max = n;
        }
        return gcd(min, max);
    }

    private int gcd(int a, int b) {
        while (b != 0) {
            int t = b;
            b = a % b;
            a = t;
        }
        return a;
    }
}
```

---

## Walking through an example

`nums = [2, 5, 6, 9, 10]`:

1. Walk through to find min and max:
   - 2: min=2, max=2
   - 5: min=2, max=5
   - 6: min=2, max=6
   - 9: min=2, max=9
   - 10: min=2, max=10
2. Call `gcd(2, 10)`:
   - b=10, a%b = 2%10 = 2, so a=10, b=2
   - b=2, a%b = 10%2 = 0, so a=2, b=0
   - Loop ends. Return 2.

`nums = [7, 5, 6, 8, 3]`:

1. min=3, max=8
2. `gcd(3, 8)`:
   - a=8, b=3, a%b = 2. Now a=3, b=2.
   - a=3, b=2, a%b = 1. Now a=2, b=1.
   - a=2, b=1, a%b = 0. Now a=1, b=0.
   - Return 1.

---

## Cost

- **Time:** O(n) for the min/max scan. The GCD is O(log(min)) because each step makes the numbers smaller.
- **Space:** O(1).

---

## What I took away from this one

This is a "two sub-problems" problem. Each sub-problem is small on its own, but they need to be combined in the right order. The structure is:
1. Reduce the input to what I actually need (just min and max)
2. Apply the right tool for that reduced input (GCD)

If I had tried to do this by checking every pair in the array, the code would have been way messier. Filtering down to the only two values that matter first, then solving the smaller problem, is much cleaner.

Also, the Euclidean algorithm came up again. I have now seen it in 1071 and here. It is one of those classics that I want to keep in my head, because it shows up in interviews and in problems where you would not expect it. The two-variable while loop with the remainder swap is short enough to memorize.
