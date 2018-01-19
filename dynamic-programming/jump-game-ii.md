## Jump Game II
_update Jan 18,2018  22:00_

---
[LeetCode](https://leetcode.com/problems/jump-game-ii/description/)

Given an array of non-negative integers, you are initially positioned at the first index of the array.

Each element in the array represents your maximum jump length at that position.

Your goal is to reach the last index in the minimum number of jumps.

**For example:**

    Given array A = [2,3,1,1,4]

The minimum number of jumps to reach the last index is 2. (Jump 1 step from index 0 to 1, then 3 steps to the last index.)

**Note:** 

You can assume that you can always reach the last index.

<br>

### Basic Idea:
* #### DP Solution (TLE):
从右往左进行dp，对于每个格子 `dp[i]`，考虑 `step from 1 to nums[i]` 的步数所到达的格子中步数最少的(minStep)，则  `dp[i] = minStep + 1`；
```java
    Induction Rule:
        

















