## Longest Increasing Subsequence
_update Sep 13,2017  22:57_

---
[LeetCode](https://leetcode.com/problems/longest-increasing-subsequence/description/)


Given an unsorted array of integers, find the length of longest increasing subsequence.

**For example,**

    Given [10, 9, 2, 5, 3, 7, 101, 18],

The longest increasing subsequence is [2, 3, 7, 101], therefore the length is 4. Note that there may be more than one LIS combination, it is only necessary for you to return the length.

Your algorithm should run in **O(n2)** complexity.

**Follow up:** Could you improve it to O(n log n) time complexity?

#### Basic Idea:
**思路 1：DP**

这是最基本的思路，Greg 在课上讲过，我又复习了一下。  
状态转移方程为：
```python
    dp[i] = max[dp[j] for j < i and nums[j] < nums[i]] + 1
```

Java Code:
```java
    class Solution {
        public int lengthOfLIS(int[] nums) {
            if (nums == null || nums.length == 0) return 0;
            int[] dp = new int[nums.length];
            dp[0] = 1;
            for (int i = 1; i < nums.length; ++i) {
                int prevMax = 0;
                for (int j = 0; j < i; ++j) {
                    if (nums[j] < nums[i]) prevMax = Math.max(prevMax, dp[j]);
                }
                dp[i] = prevMax + 1;
            }
            int ret = 0;
            for (int i = 0; i < dp.length; ++i) {
                ret = Math.max(ret, dp[i]);
            }
            return ret;
        }
    }
```