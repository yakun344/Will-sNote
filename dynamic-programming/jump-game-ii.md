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
        dp[i] = min{ dp[i + step] for step in (1 to nums[i]) } + 1;
    Base Case:
        dp[-1] = 0;
```
<br>
**Java Code:**
```java
class Solution {
    public int jump(int[] nums) {
        int[] dp = new int[nums.length];
        Arrays.fill(dp, Integer.MAX_VALUE);
        dp[nums.length - 1] = 0;
        for (int i = nums.length - 2; i >= 0; --i) {
            int minStep = Integer.MAX_VALUE;
            for (int step = 1; step <= nums[i]; ++step) {
                if (i + step < nums.length && dp[i + step] < minStep) {
                    minStep = dp[i + step];
                }
            }
            dp[i] = minStep == Integer.MAX_VALUE ? Integer.MAX_VALUE : minStep + 1;
        }
        return dp[0];
    }
}
```

















