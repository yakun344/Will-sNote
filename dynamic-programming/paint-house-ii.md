## Paint House II
_update Sep 12,2017  10:31_

---
[LeetCode](https://leetcode.com/problems/paint-house-ii/description/)

There are a row of n houses, each house can be painted with one of the k colors. The cost of painting each house with a certain color is different. You have to paint all the houses such that no two adjacent houses have the same color.

The cost of painting each house with a certain color is represented by a n x k cost matrix. For example, `costs[0][0]` is the cost of painting house 0 with color 0; `costs[1][2]` is the cost of painting house 1 with color 2, and so on... Find the minimum cost to paint all houses.

**Note:**  
All costs are positive integers.

**Follow up:  
**Could you solve it in O(nk) runtime?

#### Basic Idea:
和上面的题目类似，不同的是我们这次需要考虑 k 种颜色，所以我们需要一个 k*n 大小的数组，利用之前一题相同的状态转移方程，可以做到 O(nk^2) 时间的解，因为每次求 min 我们需要 O(k) 时间，对除每个颜色外的其他颜色分别求min，则需要 O(k^2) 时间，所以我们必须优化。

注意到每次事实上我们只需要维持第 i-1 个房子的 min 和 second min 就可以，我们用一个 size=2 的 maxheap （priority queue）来解决，如此就是 O(nk) 时间，而且空间也不需要保存 n*k 的数组，达到了O(1)space。

#### Java Code:
```java
    class Solution {
        public int minCostII(int[][] costs) {
            if (costs == null || costs.length == 0) return 0;
            int n = costs.length, k = costs[0].length;
            PriorityQueue<int[]> pq = new PriorityQueue<>(2, new Comparator<int[]>(){
                // size=2，maxheap，保持每个房子最小和第二小的颜色和cost，存入int<color，cost>
                @Override
                public int compare(int[] cost1, int[] cost2) {
                    return cost2[1] - cost1[1];
                }
            });
            // 把第一个房子所有颜色的costs入队，保持两个最小
            for (int i = 0; i < k; ++i) {
                pq.offer(new int[]{i, costs[0][i]});
                if (pq.size() > 2) pq.poll();
            }
            for (int i = 1; i < n; ++i) {
                int[] secondMin = pq.poll();
                int[] minCost = pq.poll();
                for (int color = 0; color < k; ++color) {
                    int min = minCost[0] == color ? secondMin[1] : minCost[1];
                    pq.offer(new int[]{color, min + costs[i][color]});
                    if (pq.size() > 2) pq.poll();
                }
            }
            if (pq.size() == 2) pq.poll();
            return pq.poll()[1];
        }
    }
```






















