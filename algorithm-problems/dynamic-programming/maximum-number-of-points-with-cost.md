---
description: 'Update Jul 18, 2021'
---

# Maximum Number of Points with Cost

[Leetcode](https://leetcode.com/problems/maximum-number-of-points-with-cost/)

You are given an `m x n` integer matrix `points` \(**0-indexed**\). Starting with `0` points, you want to **maximize** the number of points you can get from the matrix.

To gain points, you must pick one cell in **each row**. Picking the cell at coordinates `(r, c)` will **add** `points[r][c]` to your score.

However, you will lose points if you pick a cell too far from the cell that you picked in the previous row. For every two adjacent rows `r` and `r + 1` \(where `0 <= r < m - 1`\), picking cells at coordinates `(r, c1)` and `(r + 1, c2)` will **subtract** `abs(c1 - c2)` from your score.

Return _the **maximum** number of points you can achieve_.

`abs(x)` is defined as:

* `x` for `x >= 0`.
* `-x` for `x < 0`.

**Example 1:**![](https://assets.leetcode.com/uploads/2021/07/12/screenshot-2021-07-12-at-13-40-26-diagram-drawio-diagrams-net.png)

```text
Input: points = [[1,2,3],[1,5,1],[3,1,1]]
Output: 9
Explanation:
The blue cells denote the optimal cells to pick, which have coordinates (0, 2), (1, 1), and (2, 0).
You add 3 + 5 + 3 = 11 to your score.
However, you must subtract abs(2 - 1) + abs(1 - 0) = 2 from your score.
Your final score is 11 - 2 = 9.
```

**Example 2:**![](https://assets.leetcode.com/uploads/2021/07/12/screenshot-2021-07-12-at-13-42-14-diagram-drawio-diagrams-net.png)

```text
Input: points = [[1,5],[2,3],[4,2]]
Output: 11
Explanation:
The blue cells denote the optimal cells to pick, which have coordinates (0, 1), (1, 1), and (2, 0).
You add 5 + 3 + 4 = 12 to your score.
However, you must subtract abs(1 - 1) + abs(1 - 0) = 1 from your score.
Your final score is 12 - 1 = 11.
```

**Constraints:**

* `m == points.length`
* `n == points[r].length`
* `1 <= m, n <= 105`
* `1 <= m * n <= 105`
* `0 <= points[r][c] <= 105`

### Basic Idea:

这道题目就是要在每一行中选择一个数，但相邻两行之间选择的col的差需要减去，问最后能得到的最大分数。因为数据量比较大，我们不可能每次对于每个col，看上面一行的所有col，于是只能想一想有没有DP的思路，将 `O(C^2)` 降低到 `O(C))` 。

通过思考，我们发现可以通过DP的方法算出上一行对下一行每个col能获得的最大分数，方法就是先从左往右看，再从右往左看，每个col取最大的。例如对于如下例子：

```text
0   1   2   3   4
------------------
7   5   3   1   8    上一行（其实也是从上面一直到这一行的dp结果，每个col对应的最大分数）
------------------ 下面是dp
7   6   5   4   8    从左往右，看左边对每个col的影响，能获得的最大分数
7   6   6   7   8    从右往左，看右边的影响，和之前左边的结果取最大值

此时得到的就是每个col能得到的最大分数

-------------------
1   2   4   2   4    下面一行
-------------------
8   8   10  9   12   加上第6行的dp结果，就是下面一行最终的分数

最终只要在最后一行找到最大分数就可以了
```

#### Java Code:

```java
class Solution {
    public long maxPoints(int[][] points) {
        int R = points.length, C = points[0].length;
        long[][] dp = new long[R][C];
        for (int i = 0; i < C; ++i) dp[0][i] = points[0][i];
        
        for (int r = 1; r < R; ++r) {
            long[] last = new long[C];
            for (int c = 0; c < C; ++c) {
                if (c == 0) last[0] = dp[r - 1][0];
                else {
                    last[c] = Math.max(dp[r - 1][c], last[c - 1] - 1);
                }
            }
            for (int c = C - 1; c >= 0; --c) {
                if (c == C - 1) last[C - 1] = Math.max(last[C - 1], dp[r - 1][C - 1]);
                else {
                    last[c] = Math.max(last[c], last[c + 1] - 1);
                }
            }
            for (int c = 0; c < C; ++c) {
                dp[r][c] = points[r][c] + last[c];
            }
        }
        long ret = 0;
        for (int c = 0; c < C; ++c) {
            ret = Math.max(dp[R - 1][c], ret);
        }
        return ret;
    }
}
```

