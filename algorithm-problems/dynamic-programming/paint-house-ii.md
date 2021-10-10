# Paint House II

_update Sep 12,2017 10:31_

[LeetCode](https://leetcode.com/problems/paint-house-ii/description/)

There are a row of n houses, each house can be painted with one of the k colors. The cost of painting each house with a certain color is different. You have to paint all the houses such that no two adjacent houses have the same color.

The cost of painting each house with a certain color is represented by a n x k cost matrix. For example, `costs[0][0]` is the cost of painting house 0 with color 0; `costs[1][2]` is the cost of painting house 1 with color 2, and so on... Find the minimum cost to paint all houses.

**Note:**\
All costs are positive integers.

**Follow up:  **\
****Could you solve it in O(nk) runtime?

## Basic Idea:

和上面的题目类似，不同的是我们这次需要考虑 k 种颜色，所以我们需要一个 k\*n 大小的数组，利用之前一题相同的状态转移方程，可以做到 O(nk^2) 时间的解，因为每次求 min 我们需要 O(k) 时间，对除每个颜色外的其他颜色分别求min，则需要 O(k^2) 时间，所以我们必须优化。

注意到每次事实上我们只需要维持第 i-1 个房子的 min 和 second min 就可以，我们用一个 size=2 的 maxheap （priority queue）来解决，如此就是 O(nk) 时间，而且空间也不需要保存 n\*k 的数组，达到了O(1)space。

## Java Code:

```java
// O(n*k^2) 解法
class Solution {
    public int minCostII(int[][] costs) {
        int k = costs[0].length;
        int n = costs.length;
        
        int[][] dp = new int[k][n];
        for (int i = 0; i < dp.length; ++i) {
            for (int j = 0; j < dp[0].length; ++j) {
                dp[i][j] = Integer.MAX_VALUE;
            }
        }
        for (int i = 0; i < k; ++i) dp[i][0] = costs[0][i];
        for (int i = 1; i < n; ++i) {
            for (int j = 0; j < k; ++j) {
                for (int m = 0; m < k; ++m) {
                    if (m != j) {
                        dp[j][i] = Math.min(dp[j][i], dp[m][i - 1] + costs[i][j]);
                    }
                }
            }
        }
        int ret = Integer.MAX_VALUE;
        for (int i = 0; i < k; ++i) {
            ret = Math.min(ret, dp[i][n - 1]);
        }
        return ret;
    }
}

// O(nk) 解法
class Solution {
    public int minCostII(int[][] costs) {
        int n = costs.length;
        int k = costs[0].length;
        PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> {
            return Integer.compare(b[0], a[0]); // size 2 max heap
        });
        for (int i = 0; i < k; ++i) {
            pq.offer(new int[] {costs[0][i], i});
            if (pq.size() > 2) {
                pq.poll();
            }
        }
        // 依次考虑每个房子，pq中保存上一个房子最小cost的两个颜色以及cost
        for (int i = 1; i < n; ++i) {
            int[] second = pq.poll();
            int[] min = pq.poll();
            for (int j = 0; j < k; ++j) {
                int minCost = (j == min[1] ? second[0] : min[0]) + costs[i][j];
                pq.offer(new int[] {minCost, j});
                if (pq.size() > 2) {
                    pq.poll();
                }
            }
        }
        // 最后pq中保存的最小cost就是解
        pq.poll();
        return pq.poll()[0];
    }
}
```
