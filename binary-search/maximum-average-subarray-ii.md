## Maximum Average Subarray II
_update Aug 17, 2017 18:55_

---
[LeetCode](https://leetcode.com/problems/maximum-average-subarray-ii/description/)   
[Lintcode](https://www.lintcode.com/problem/maximum-average-subarray-ii/description)

Given an array consisting of n integers, find the contiguous subarray whose length is greater than or equal to k that has the maximum average value. And you need to output the maximum average value.

**Example 1:**

Input: [1,12,-5,-6,50,3], k = 4
Output: 12.75

**Explanation:**

when length is 5, maximum average value is 10.8,
when length is 6, maximum average value is 9.16667.
Thus return 12.75.

**Note:**    

  1.  `1 <= k <= n <= 10,000.`
  2.  Elements of the given array will be in range [-10,000, 10,000].
  3.  The answer with the calculation error less than 10-5 will be accepted.

#### Basic Idea:
因为题目要求是长度不小于k的连续子序列的最大平均值，这个问题直接解的话只能是O(n^2)的时间，需要遍历每个子序列。为了加快速度，我们选择使用对答案进行二分，猜测答案然后验证的算法。

对答案的二分大概会执行最多几十次（因为`log2(MAX_INT) = 32`），每次验证消耗时间为O(n)，所以这种算法最终的时间复杂度为O(n)。

其中，验证一个数组中是否存在长度不小于 k 的子数组平均值不小于 target，我们可以先将原数组减去 target，然后验证是否有长度不小于 k 的子数组的和大于等于 0。后者可以在O(n)的时间内办到。

#### Java Code:
```java
    // 先找出min和max，然后在范围内使用二分法猜测最大平均值的值，然后验证该值是否存在
    // 二分时间其实很快，即使是最大int，也只有O(32)，每次验证耗时O(n)
    class Solution {
        public double findMaxAverage(int[] nums, int k) {
            int min = Integer.MAX_VALUE;
            int max = Integer.MIN_VALUE;
            for (int num : nums) {
                min = Math.min(min, num);
                max = Math.max(max, num);
            }
            double p = min, r = max;
            while (r - p > 10e-6) {
                double q = (p + r) / 2;
                if (isValid(nums, q, k)) p = q;
                else r = q;
            }
            return r;
        }
        // 验证的时候，把原数组减去avg的值，然后利用前缀和。
        // 只要存在长度不小于k的子数组的和大于等于0即可说明valid
        private boolean isValid(int[] nums, double avg, int k) {
            double[] sums = new double[nums.length + 1];
            double minSum = Integer.MAX_VALUE;
            for (int i = 1; i < sums.length; ++i) {
                sums[i] = sums[i - 1] + nums[i - 1] - avg;
                if (i >= k) {
                    minSum = Math.min(sums[i - k], minSum);
                    if (sums[i] - minSum >= 0) return true;
                }
            }
            return false;
        }
    }
```

---
_update Dec 1, 2017 12:58_

#### 更新
之前的 Java code 仍然感觉写的很好，尤其是 `isValid()` 函数。再更新一个Python的解，但是由于 LeetCode 的 OJ 设定问题，对于python的时间too tight，这个 O(c*n) (c 大概为几十) 的解会 TLE：
```python
    # 用二分结果的方法，对 MAX_VALUE 进行二分，对每个可能的结果进行验证，看其
    # 是否可以出现。检验方法就是先把nums中所有数减去targetAvg，然后看有没有长
    # 度大于等于 k 的连续子数组的和 大于零。这个过程耗时 O(n)，方法是从左到右
    # 遍历，跟踪距离当前 currIndex 超过 k 之前的最小值，当出现 当前元素 - min
    # >= 0, 即返回true
    class Solution(object):
        def findMaxAverage(self, nums, k):
            """
            :type nums: List[int]
            :type k: int
            :rtype: float
            """
            p, r = min(nums), max(nums)
            while r - p > 1e-6:
                q = (p + r) / 2.0
                if self.isValid(nums, k, q):
                    p = q
                else:
                    r = q
            return p



        def isValid(self, nums, k, targetAvg):
            preSum = [0] * (len(nums) + 1)
            minSum = float('inf')
            for i in range(len(nums)):
                preSum[i + 1] = preSum[i] + nums[i] - targetAvg
                if i >= k - 1:
                    minSum = min(minSum, preSum[i - k  + 1])
                    if preSum[i + 1] - minSum >= 0:
                        return True
            return False
```
