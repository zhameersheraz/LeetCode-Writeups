# 1260. Shift 2D Grid

**Category:** Daily Question
**Difficulty:** Easy
**Platform:** LeetCode

Writeup by: zham

## Description

> Given a 2D `grid` of size `m x n` and an integer `k`. You need to shift the `grid` `k` times.
>
> In one shift operation:
> - Element at `grid[i][j]` moves to `grid[i][j + 1]`.
> - Element at `grid[i][n - 1]` moves to `grid[i + 1][0]`.
> - Element at `grid[m - 1][n - 1]` moves to `grid[0][0]`.
>
> Return the 2D `grid` after applying shift operation `k` times.

## How I thought about it

The three bullet points that describe one shift describe a single linear shift through the grid, treating it as a row-major flattened array. The last cell wraps to the first, the row ends wrap into the next row's start. So a "shift" here is the same as rotating a 1D array, just with a 2D view at the end.

That flattened view is the key insight. Once I see the shift as moving each cell `k` positions forward in a 1D array of length `m * n`, with wrap-around, the problem becomes a one-liner: new_index = (old_index + k) % total. Then I rebuild the 2D grid from the 1D result.

Two things to watch:
1. `k` can be bigger than the total cell count, so I take `k % total` first. No point doing full rotations.
2. The indexing formula uses `i * n + j` to flatten, and `(oldIdx + k) % total` for the new spot.

## Solution

```java
import java.util.*;

class Solution {
    public List<List<Integer>> shiftGrid(int[][] grid, int k) {
        int m = grid.length;
        int n = grid[0].length;
        int total = m * n;
        k = k % total;
        
        int[] flat = new int[total];
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                int oldIdx = i * n + j;
                int newIdx = (oldIdx + k) % total;
                flat[newIdx] = grid[i][j];
            }
        }
        
        List<List<Integer>> result = new ArrayList<>();
        for (int i = 0; i < m; i++) {
            List<Integer> row = new ArrayList<>();
            for (int j = 0; j < n; j++) {
                row.add(flat[i * n + j]);
            }
            result.add(row);
        }
        
        return result;
    }
}
```

## Walking through an example

Input: `grid = [[1,2,3],[4,5,6],[7,8,9]]`, `k = 1`

Flatten the grid (row-major): `[1, 2, 3, 4, 5, 6, 7, 8, 9]`

Total cells = 9, so `k = 1 % 9 = 1`. Each cell moves forward by 1, with wrap-around.

| old index | value | new index = (old + 1) % 9 |
|-----------|-------|---------------------------|
| 0         | 1     | 1                         |
| 1         | 2     | 2                         |
| 2         | 3     | 3                         |
| 3         | 4     | 4                         |
| 4         | 5     | 5                         |
| 5         | 6     | 6                         |
| 6         | 7     | 7                         |
| 7         | 8     | 8                         |
| 8         | 9     | 0                         |

Resulting flat array: `[9, 1, 2, 3, 4, 5, 6, 7, 8]`

Reconstruct into 2D:
- Row 0: `[9, 1, 2]`
- Row 1: `[3, 4, 5]`
- Row 2: `[6, 7, 8]`

Output: `[[9,1,2],[3,4,5],[6,7,8]]` ✓

## Cost

- **Time:** O(m * n) for the flatten + O(m * n) for the reconstruct, so O(m * n) overall.
- **Space:** O(m * n) for the flat array. The result list itself is O(m * n) too, but that is required output.

## What I took away

The flatten-and-shift trick is the right move any time a 2D problem describes movement in a single linear order (left-to-right, then top-to-bottom). Once flattened, the problem becomes the 1D "rotate array by k" problem, which is solved with modular arithmetic in one line. The wraparound condition (`newIdx = (oldIdx + k) % total`) is the same expression I would use to rotate a circular buffer. I also want to remember the `k % total` step. Skipping it works for small inputs, but for larger k it just wastes time doing full rotations.
