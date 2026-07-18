# 605. Can Place Flowers

Writeup by: zham

- **Difficulty:** Easy
- **Topic:** Array, Greedy
- **Platform:** LeetCode
- **Language:** C++

---

## Description

> You have a long flowerbed in which some of the plots are planted, and some are not. However, flowers cannot be planted in **adjacent** plots.
>
> Given an integer array `flowerbed` containing `0`'s and `1`'s, where `0` means empty and `1` means not empty, and an integer `n`, return `true` if `n` new flowers can be planted in the `flowerbed` without violating the no-adjacent-flowers rule and `false` otherwise.
>
> **Example 1:**
>
> ```
> Input: flowerbed = [1,0,0,0,1], n = 1
> Output: true
> ```
>
> **Example 2:**
>
> ```
> Input: flowerbed = [1,0,0,0,1], n = 2
> Output: false
> ```
>
> **Constraints:**
>
> - `1 <= flowerbed.length <= 2 * 10^4`
> - `flowerbed[i]` is `0` or `1`.
> - There are no two adjacent flowers in `flowerbed`.
> - `0 <= n <= flowerbed.length`

---

## How I thought about it

The rule is simple: no two flowers next to each other. So if I want to plant a new flower at spot `i`, both `i-1` and `i+1` have to be empty (or not exist, when `i` is at the edge).

The greedy move is to walk left to right and plant at every empty spot that has empty neighbors. Each plant uses up the most space locally, but it also never blocks another valid spot. So it gives the maximum number of flowers possible.

I also plant as I go. Marking `flowerbed[i] = 1` after planting is important because then the next spot to the right will see it as "not empty" and skip itself. Without that, the loop would plant flowers next to each other.

Edge case: at index 0, there is no left neighbor, so just check the right. At the last index, no right neighbor, so just check the left. The `(i == 0) || (flowerbed[i-1] == 0)` trick handles that without an `if` outside the loop.

---

## Solution

```cpp
class Solution {
public:
    bool canPlaceFlowers(vector<int>& flowerbed, int n) {
        int count = 0;
        int size = flowerbed.size();
        for (int i = 0; i < size; i++) {
            if (flowerbed[i] == 0) {
                bool leftEmpty = (i == 0) || (flowerbed[i-1] == 0);
                bool rightEmpty = (i == size-1) || (flowerbed[i+1] == 0);
                if (leftEmpty && rightEmpty) {
                    flowerbed[i] = 1;
                    count++;
                    if (count >= n) return true;
                }
            }
        }
        return count >= n;
    }
};
```

---

## Walking through an example

`flowerbed = [1,0,0,0,1]`, `n = 1`:

- i=0: plot is 1, skip.
- i=1: plot is 0. Left is 1, not empty. Skip.
- i=2: plot is 0. Left is 0, right is 0. Plant. `flowerbed` becomes `[1,0,1,0,1]`. `count = 1`. `1 >= 1`, return `true`.

`flowerbed = [1,0,0,0,1]`, `n = 2`:

- i=0: skip.
- i=1: left is 1, skip.
- i=2: left 0, right 0, plant. `count = 1`.
- i=3: left is 1, skip.
- i=4: skip.
- End. `count = 1`, not enough, return `false`.

---

## Cost

- **Time:** O(n) for the single pass.
- **Space:** O(1), just a couple of variables. The flowerbed is modified in place.

---

## What I took away from this one

The big idea is "plant as soon as you can". Greedy works here because planting a flower in the leftmost valid spot never makes things worse. The newly planted flower blocks only the spot directly to its right, which is one spot we couldn't have used anyway. So we lose nothing by being greedy.

The edge check `(i == 0) || (flowerbed[i-1] == 0)` is a nice little trick. Treating the boundary as "automatically empty" saves a special case at the start of the loop. Same on the right side.

I also learned to mark the spot as planted right after putting a flower there. Without that, the next iteration would think the right neighbor is still empty and try to plant there too. That would have broken the no-adjacent rule and given the wrong answer.
