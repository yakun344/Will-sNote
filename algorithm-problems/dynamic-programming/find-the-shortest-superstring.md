# Find the Shortest Superstring

_update Feb 18 2019, 20:15_

[LeetCode](https://leetcode.com/problems/find-the-shortest-superstring/)

Given an array A of strings, find any smallest string that contains each string in A as a substring.

We may assume that no string in A is substring of another string in A.

**Example 1:**

```text
Input: ["alex","loves","leetcode"]
Output: "alexlovesleetcode"
Explanation: All permutations of "alex","loves","leetcode" would also be accepted.
```

**Example 2:**

```text
Input: ["catg","ctaagt","gcta","ttca","atgcatc"]
Output: "gctaagttcatgcatc"
```

**Note:**

1. `1 <= A.length <= 12`
2. `1 <= A[i].length <= 20`

## Basic Idea:

先上花花酱讲题的链接：[https://www.youtube.com/watch?v=u\_Wc4jwrp3Q](https://www.youtube.com/watch?v=u_Wc4jwrp3Q)

这是他视频中的slide截图，感觉很不错：

![](../../.gitbook/assets/screenshot-2019-02-18-20.23.36.png)

这道题本质上可以被看作哈密顿路径问题（hamiltonian path），因为我们可以将这道题的定义进行抽象：给定N个节点，每对节点之间的边有一定的weight，要求找到一条路径经过每个节点有且只有一次，且该路径总weight最小。

具体在这里，string a 和 string b 之间的 weight 就是将 b append 到 a 之后需要增加的长度。

这种问题brute force的做法是DFS，时间复杂度为 `O(N!)`，如果使用DP，可以利用更多的空间而使时间优化到 `O(2^N)`级别。这里的优化其实是显著的，Leetcode中 `O(N!)` 复杂度大概最多做到 `N=10`，而 `O(2^N)` 是可以做到 `N=20` 的。

具体的方法是DP，比较特殊的是我们需要DP数组记录不同的状态。其实在搜索路径过程中每个状态可以用它已经经过的Nodes和结尾的Node来唯一确定。只要这两者确定，我们不需要关心这些被经过的Node之间的相互顺序，这样就将排列问题优化为了一个组合问题。一般来说用一个int作为bitmap就可以表示当前路径已经经过的node（将相应位置标记为1），所以这里用到的dp数组是 `int dp[1<<N][N][2]`，`dp[s][i][0]`表示在当前已选择node集合为 `s` 并且该路径目前以 `i` 结尾的最短长度， `dp[s][i][1]` 则记录上一个node的index用来最终恢复路径。

### Java Code:

```java
class Solution {
    public String shortestSuperstring(String[] A) {
        int V = A.length;
        // 算每个string a和其他string b相连之后增加的 length
        int[][] dist = new int[V][V];
        for (int i = 0; i < V; ++i) {
            for (int j = 0; j < V; ++j) {
                String a = A[i], b = A[j]; // 拼ab，算长度
                int commonLen = Math.min(a.length(), b.length());
                while (commonLen > 0 && 
                       !a.substring(a.length() - commonLen).equals(b.substring(0, commonLen))) {
                    commonLen--;
                }
                dist[i][j] = b.length() - commonLen;
                // System.out.println(a + " " + b + " " + dist[i][j]);
            }
        }

        // dp[i][j] 表示以 binary mask i 表示的前缀节点组合并且以 A[j] 结尾的路径，
        // 其最小长度为 dp[i][j][0]
        // dp[i][j][1] 存放的是parent的index，因为我们最终要找到一条最短路径
        int[][][] dp = new int[1<<V][V][2];

        // initialization
        for (int i = 0; i < dp.length; ++i) {
            for (int j = 0; j < V; ++j) {
                dp[i][j][0] = Integer.MAX_VALUE / 2;
                dp[i][j][1] = -1;
            }
        }
        for (int i = 0; i < V; ++i) {
            dp[1<<i][i][0] = A[i].length();
        }

        // 每种前缀节点组合
        for (int s = 0; s < (1<<V); ++s) {
            // 考虑每个node作为当前结尾
            for (int i = 0; i < V; ++i) {
                if (((1 << i) & s) == 0) continue; // 该组合s中没有node i
                int prev = s & (~(1 << i)); // 之前的组合，不包括i
                // 考虑每个node为之前的结尾
                for (int j = 0; j < V; ++j) {
                    if (i == j || ((1 << j) & prev) == 0) continue;
                    if (dp[prev][j][0] + dist[j][i] < dp[s][i][0]) {
                        dp[s][i][0] = dp[prev][j][0] + dist[j][i];
                        dp[s][i][1] = j;
                    }
                    // System.out.println(i + " " + A[i] + " " + A[j] + " " + dp[s][i][0] + " " + dist[j][i]);
                }
            }
        }

        // 在dp[(1<<V)-1]（即所有node都被使用的组合）中找出最小的长度，并利用dp[i][j][1]得到path
        int minLen = Integer.MAX_VALUE / 2, minEndNode = -1, s = (1 << V) - 1;
        for (int i = 0; i < V; ++i) {
            if (dp[s][i][0] < minLen) {
                minLen = dp[s][i][0];
                minEndNode = i;
            }
        }

        String res = "";
        int curr = minEndNode;
        // System.out.println(A[minEndNode] + " " + minLen);
        while (s > 0) {
            int prev = dp[s][curr][1];
            if (prev < 0) {
                res = A[curr] + res;
                return res;
            } else {
                int len = dist[prev][curr];
                res = A[curr].substring(A[curr].length() - len) + res;
            }
            s &= ~(1 << curr);
            curr = prev;
        }
        return res;
    }
}
```

