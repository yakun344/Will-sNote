## Min Cost Climbing Stairs
_update Jan 17, 2018 18:46_

---
[LeetCode](https://leetcode.com/problems/min-cost-climbing-stairs/description/) 

On a staircase, the i-th step has some non-negative cost cost[i] assigned (0 indexed).

Once you pay the cost, you can either climb one or two steps. You need to find minimum cost to reach the top of the floor, and you can either start from the step with index 0, or the step with index 1.

**Example 1:**

    Input: cost = [10, 15, 20]
    Output: 15
    Explanation: Cheapest is start on cost[1], pay that cost and go to the top.

**Example 2:**

    Input: cost = [1, 100, 1, 1, 1, 100, 1, 1, 100, 1]
    Output: 6
    Explanation: Cheapest is start on cost[0], and only step on 1s, skipping cost[3].

**Note:**  

1. cost will have a length in the range [2, 1000].
2. Every cost[i] will be an integer in the range [0, 999].

<br>

### Basic Idea:
&emsp; 这道题可以算作是 [Climbing Stairs](https://will-gxz.gitbooks.io/xiaozheng_algo/content/dynamic-programming/climbing-stairs.html) 的 follow up，虽然同属于 easy，但难度的确要高一些，考虑的内容也略有不同。

&emsp; 思路上自然也是使用 DP，因为问题所涉及到的是一个一维input array的最大最小值问题，优先考虑DP。我们注意到对于到达某个位置 i 所需要的 cost：`dp[i]` 可以用到达它之前一步或者两步位置的最小cost来表示，即我们可以有如下 induction rule:
```java
    Induction Rule:
        dp[i] = min{ dp[i-2]+cost[i-2], dp[i-1]+cost[i-1] }
    Base Case:
        dp[0] = 0, dp[1] = 0, 因为可以从 0-th 或者 1-th 出发
```
时间复杂度也是 `O(n)`, 因为只需要一次 linear scan，空间复杂度 `O(n)`, 可以优化为 `O(1)`;

### Java Code:
```java












        