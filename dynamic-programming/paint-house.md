## Paint House
_update Sep 11, 2017  22:00_

---
[LeetCode](https://leetcode.com/problems/paint-house/discuss/)

There are a row of n houses, each house can be painted with one of the three colors: red, blue or green. The cost of painting each house with a certain color is different. You have to paint all the houses such that no two adjacent houses have the same color.

The cost of painting each house with a certain color is represented by a n x 3 cost matrix. For example, `costs[0][0]` is the cost of painting house 0 with color red; `costs[1][2]` is the cost of painting house 1 with color green, and so on... Find the minimum cost to paint all houses.

**Note:**

All costs are positive integers.

#### Basic Idea:
**思路 1：Memorized Search （dfs with memo）**  
**！！！  Got TLE   ！！！**

Java Code：
```java
    class Solution {
        public int minCost(int[][] costs) {
            if (costs == null || costs.length == 0) return 0;
            return dfs(costs, 0, -1, 0, new HashMap<String, Integer>());
        }
        private int dfs(int[][] costs, int pos, int lastColor, int currCost, Map<String, Integer> memo) {
            if (pos == costs.length) return currCost;
            String key = pos + "," + lastColor + "," + currCost;
            if (memo.containsKey(key)) return memo.get(key);
            int ret = Integer.MAX_VALUE;
            for (int i = 0; i < 3; ++i) {
                if (i == lastColor) continue;
                ret = Math.min(ret, dfs(costs, pos + 1, i, currCost + costs[pos][i], memo));
            }
            memo.put(key, ret);
            return ret;
        }
    }
```

**思路 2：DP**
连记忆化搜索都搞不定TLE，就只有DP一条路可以走了，就索性按DP的路子想一想。

1.  对于第 i 个房子，我们有三种选择，我们把所有房子的三种可能状态分别记为一个数组，即 `red[], green[], blue[]`;
2.  rgb三种状态 0 位置的初始值分别就是 `costs[0][0], costs[0][1], costs[0][2]`；
3.  例如对于第 i 个房子的 red 状态，就有`red[i] = costs[i][0] + min(blue[i-1], green[i-1])`, 对于 blue 和 green 以此类推，这就是我们的状态转移方程；

Java Code:
```java
    class Solution {
        public int minCost(int[][] costs) {
            if (costs == null || costs.length == 0) return 0;
            int n = costs.length;
            int[] red = new int[n], green = new int[n], blue = new int[n];
            red[0] = costs[0][0];
            green[0] = costs[0][1];
            blue[0] = costs[0][2];
            for (int i = 1; i < n; ++i) {
                red[i] = costs[i][0] + Math.min(green[i - 1], blue[i - 1]);
                green[i] = costs[i][1] + Math.min(red[i - 1], blue[i - 1]);
                blue[i] = costs[i][2] + Math.min(red[i - 1], green[i - 1]);                                  
            }
            return Math.min(red[n - 1], Math.min(blue[n - 1], green[n - 1]));
        }
    }
```










    
    
    