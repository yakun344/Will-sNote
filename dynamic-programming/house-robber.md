## House Robber 

[Leetcode](https://leetcode.com/problems/house-robber/#/description)
You are a professional robber planning to rob houses along a street. Each house has a certain amount of money stashed, the only constraint stopping you from robbing each of them is that **adjacent houses** have security system connected and it will automatically contact the police if two adjacent houses were broken into on the same night.

Given a list of non-negative integers representing the amount of money of each house, determine the maximum amount of money you can rob tonight without alerting the police.

---

这是leetcode上的一道easy题目，涉及到基础的dp。对于输入数组v[ ]，求其子序列的最大和，这个子序列必须满足其中不存在在v中相邻的两个元素。
根据dp的思想，我们可以归纳 sub problem： `dp[i] = index <= i 的最大和`，且`dp[i] = max{dp[i - 1], dp[i - 2] + v[i]}`。即对于每个房子，我们要考虑是否抢劫，如果抢，那么就是`dp[i - 2]`加当前房子价值，如果不抢，则收益就等于`dp[i - 1]`。
除了subproblem的递推公式之外，我们还需要写出基本起始条件，详情见下面的code:
```java
    public int rob(int[] nums) {
        if (nums.length == 0) return 0;
        if (nums.length == 1) return nums[0];
        int[] dp = new int[nums.length];
        dp[0] = nums[0];
        dp[1] = Math.max(nums[0], nums[1]);
        for (int i = 2; i < nums.length; ++i) {
            dp[i] = Math.max(dp[i - 1], dp[i - 2] + nums[i]);
        }
        return dp[dp.length - 1];
    }
```
我们注意到得出每个dp[i]我们实际上只需要前面两个元素，所以我们可以把数组dp[ ]简化为两个变量，从而实现`O(1)`的space complexity:
```java
        public int rob(int[] nums) {
            if (nums.length == 0) return 0;
            if (nums.length == 1) return nums[0];
            int dp0 = nums[0];
            int dp1 = Math.max(nums[0], nums[1]);
            for (int i = 2; i < nums.length; ++i) {
                int curr = Math.max(dp0 + nums[i], dp1);
                dp0 = dp1;
                dp1 = curr;
            }
            return dp1;
        }
```