## Maximum Average Subarray
_update Aug 17, 2017  18:51_

---
[LeetCode](https://leetcode.com/problems/maximum-average-subarray-i/description/)

Given an array consisting of n integers, find the contiguous subarray of given length k that has the maximum average value. And you need to output the maximum average value.

**Example 1:**

    Input: [1,12,-5,-6,50,3], k = 4
    Output: 12.75
Explanation: Maximum average is (12-5-6+50)/4 = 51/4 = 12.75

**Note:**
1 <= k <= n <= 30,000.
Elements of the given array will be in the range [-10,000, 10,000].

#### Basic Idea:
比较好的有两种思路，前缀和 以及 sliding window。时间复杂度都是O(n)。这里贴一个前缀和的java Code。

此题的 follow up 就是 [Maximum Average Subarray II](https://will-gxz.gitbooks.io/xiaozheng_algo/content/linkedlist-and-array/maximum-average-subarray-ii.html)，条件变成了长度不小于 k 的连续子序列的最大平均值。

Java Code:
```java
    class Solution {
        public double findMaxAverage(int[] nums, int k) {
            int maxSum = Integer.MIN_VALUE;
            int[] sums = new int[nums.length + 1];
            for (int i = 1; i < sums.length; ++i) {
                sums[i] = sums[i - 1] + nums[i - 1];
                if (i >= k) maxSum = Math.max(maxSum, sums[i] - sums[i - k]);
            }
            return maxSum / (k * 1.0);
        }
    }
```