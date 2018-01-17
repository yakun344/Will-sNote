## Climbing Stairs
_update Jan 17, 2018  18:40_

---
[LeetCode](https://leetcode.com/problems/climbing-stairs/description/)

You are climbing a stair case. It takes n steps to reach to the top.

Each time you can either climb 1 or 2 steps. In how many distinct ways can you climb to the top?

**Note:** Given n will be a positive integer.


**Example 1:**

        Input: 2
        Output:  2
        Explanation:  There are two ways to climb to the top.
        
        1. 1 step + 1 step
        2. 2 steps
        
**Example 2:**

        Input: 3
        Output:  3
        Explanation:  There are three ways to climb to the top.
        
        1. 1 step + 1 step + 1 step
        2. 1 step + 2 steps
        3. 2 steps + 1 step
        
### Basic Idea:
使用 DP 的思路考虑，到达第 i 阶的可能步数 `dp[i] = dp[i - 1] + dp[i - 2]`, 即等于这次通过一步或者两步到达这里的起始点的最多步数和；

#### Pyhton3 Code:
```python
