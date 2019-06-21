# House Robber

[Leetcode](https://leetcode.com/problems/house-robber/#/description) You are a professional robber planning to rob houses along a street. Each house has a certain amount of money stashed, the only constraint stopping you from robbing each of them is that **adjacent houses** have security system connected and it will automatically contact the police if two adjacent houses were broken into on the same night.

Given a list of non-negative integers representing the amount of money of each house, determine the maximum amount of money you can rob tonight without alerting the police.

  这是leetcode上的一道easy题目，涉及到基础的dp。对于输入数组 `v[ ]`，求其子序列的最大和，这个子序列必须满足其中不存在在 v 中相邻的两个元素。  
  根据dp的思想，我们可以归纳 sub problem： `dp[i] = index <= i 的最大和`，且`dp[i] = max{dp[i - 1], dp[i - 2] + v[i]}`。即对于每个房子，我们要考虑是否抢劫，如果抢，那么就是`dp[i - 2] + 当前房子价值`，如果不抢，则收益就等于`dp[i - 1]`。 除了subproblem的递推公式之外，我们还需要写出基本起始条件, 即 `dp[0] = v[0], dp[1] = max( v[0], v[1] )`，详情见下面的code:

```java
    // java
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

我们注意到得出每个dp\[i\]我们实际上只需要前面两个元素，所以我们可以把数组dp\[ \]简化为两个变量`dp0`和`dp1`，从而实现`O(1)`的space complexity:

```java
    // java
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

_Jun 9, 2017_

最近开始看MIT的算法公开课，关于`DP`的思路有些新的想法，在这里更新一下。 之前的做法属于 `bottom-up`，这种做法速度快，节省空间，但有时候不够 intuitive，可能不容易第一时间做出来。而根据 subproblem 的地推关系式，我们往往希望可以直接用递归的方式实现，这样更加容易理解。但是递归的 time 往往是 exponential 的，因为会重复计算很多同样的子问题，解决方法就是传入一个 `dictionary(ie. HashTable)`，每次调用递归的时候，先看 table 中是否已经有了答案，如果没有再调用递归。得出每个递归的答案之后，先将其存入 table 再返回。 接下来看 Code （Python3）：

```python
    # python 3
    class Solution(object):
        def rob(self, nums):
            """
            :type nums: List[int]
            :rtype: int
            """
            if not nums: return 0
            def dp(v, n, table):
                    # 先检查是否在 table 中
                if n in table:
                    return table[n]
                if n == 0:
                    table[0] = v[0]
                    return table[0]
                elif n == 1:
                    table[1] = max(v[0], v[1])
                    return table[1]
                else:
                    table[n] = max(dp(v, n - 2, table) + v[n], dp(v, n - 1, table))
                    return table[n]

            table = {}
            return dp(nums, len(nums) - 1, table)
```

Jun 23, 2017

