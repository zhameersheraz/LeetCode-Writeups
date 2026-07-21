# 3499. Maximize Active Section with Trade I

**Category:** Daily Question
**Difficulty:** Medium
**Platform:** LeetCode

Writeup by: zham

## Description

> You are given a binary string `s` of length `n`, where:
>
> - `'1'` represents an **active** section.
> - `'0'` represents an **inactive** section.
>
> You can perform **at most one trade** to maximize the number of active sections in `s`. In a trade, you:
>
> - Convert a contiguous block of `'1'`s that is surrounded by `'0'`s to all `'0'`s.
> - Afterward, convert a contiguous block of `'0'`s that is surrounded by `'1'`s to all `'1'`s.
>
> Return the **maximum** number of active sections in `s` after making the optimal trade.
>
> **Note:** treat `s` as if it is **augmented** with a `'1'` at both ends, forming `t = '1' + s + '1'`. The augmented `'1'`s do **not** contribute to the final count.

## How I thought about it

The two-step trade is the heart of this. Step 1 takes a 1-block, and step 2 expands a 0-block into 1s. If I sit with that for a second, the net effect is: I delete a 1-block and create a bigger 1-block in its place (the 0-block I flipped has the original 1-block embedded in it).

The flip and the surrounding 0-blocks are what matters. When I remove a 1-block, the 0-block on its left and the 0-block on its right become one big 0-block. The merged 0-block has size `left_zero + one_block + right_zero`. When I then flip that merged 0-block to 1s, the gain in 1s is the merged size minus the size of the 1-block I removed. The 1-block size cancels out, so the gain is just `left_zero + right_zero`. That is the key simplification.

Now I just need to find the interior 1-block in `t` that maximizes `left_zero + right_zero`. The augmentation with `1` at both ends of `s` makes the edge cases clean: any 1-block touching the very start or the very end of `t` is "not surrounded by 0s" (it touches the boundary), so I cannot flip it. The 1-block has to be strictly interior, meaning both its neighbors are 0-blocks.

So the algorithm is: augment `s` to `t`, parse `t` into alternating runs of 0s and 1s, and for every interior 1-run, compute the sum of its two adjacent 0-run sizes. The maximum of those sums is the gain. Add that gain to the original count of 1s in `s` and return.

## Solution

```java
import java.util.*;

class Solution {
    public int maxActiveSectionsAfterTrade(String s) {
        int n = s.length();
        int onesCount = 0;
        for (char c : s.toCharArray()) {
            if (c == '1') onesCount++;
        }
        
        int tLen = n + 2;
        char[] t = new char[tLen];
        t[0] = '1';
        for (int i = 0; i < n; i++) t[i + 1] = s.charAt(i);
        t[tLen - 1] = '1';
        
        List<Integer> sizes = new ArrayList<>();
        List<Character> types = new ArrayList<>();
        
        int idx = 0;
        while (idx < tLen) {
            char c = t[idx];
            int start = idx;
            while (idx < tLen && t[idx] == c) idx++;
            sizes.add(idx - start);
            types.add(c);
        }
        
        int maxGain = 0;
        int numBlocks = sizes.size();
        for (int b = 1; b < numBlocks - 1; b++) {
            if (types.get(b) == '1') {
                int gain = sizes.get(b - 1) + sizes.get(b + 1);
                maxGain = Math.max(maxGain, gain);
            }
        }
        
        return onesCount + maxGain;
    }
}
```

## Walking through an example

Input: `s = "0100"`

Augment: `t = "101001"` (length 6)

Parse `t` into alternating runs:

| Block index | Type | Positions | Size |
|-------------|------|-----------|------|
| 0           | 1    | 0         | 1    |
| 1           | 0    | 1         | 1    |
| 2           | 1    | 2         | 1    |
| 3           | 0    | 3-4       | 2    |
| 4           | 1    | 5         | 1    |

Original 1-count in `s` = 1 (only `s[1]` is `'1'`).

Iterate interior 1-blocks (`b = 1` to `numBlocks - 2 = 3`):
- `b = 2` (type 1, size 1): left 0-block size = 1, right 0-block size = 2. Gain = 1 + 2 = 3.

Max gain = 3. Result = 1 + 3 = 4.

Verification by hand:
- Step 1: flip the 1-block at `t[2]` (the single `'1'` surrounded by `'0'`s) to `'0'`. `t` becomes `"100001"`.
- Step 2: flip the 0-block `t[1..4]` (`"0000"`, surrounded by `'1'`s) to `'1'`. `t` becomes `"111111"`.
- Strip the augmented `'1'`s. `s'` = `"1111"`. 1-count = 4. Matches.

## Cost

- **Time:** O(n). Augment is O(n), block parsing is O(n), and the gain scan is O(n) over a small bounded list.
- **Space:** O(n) for the augmented `t` array and the two parallel block lists. The block lists hold at most `n + 2` entries combined, so they are linear in `n`.

## What I took away

The augmentation trick is the kind of thing I want to remember for string problems with edge cases. By padding both ends with a known character, the boundary behavior becomes uniform: "interior" means strictly between index 0 and index `n + 1`, and any "edge" 1-block that includes the padding is automatically disqualified from being flippable because it touches the boundary. That removes a pile of special cases in the loop.

The second lesson is the algebraic simplification. The naive reading of the gain is `merged_size - one_block_size`, which is `left + one + right - one = left + right`. I worked that out by hand, but the moment I see it, the answer is just two adjacent block sizes. Whenever a problem has a cancellation like that, the implementation becomes trivial. The hard part is realizing the cancellation exists, not coding it.
