## Maximum Average Subarray
_update Jul 11, 2017 12:09_

---
[Lintcode](http://www.lintcode.com/en/problem/maximum-average-subarray/)

Given an array with positive and negative numbers, find the maximum average subarray which length should be greater or equal to given length k.

** Notice**

It's guaranteed that the size of the array is greater or equal to k.

    Example
    Given nums = [1, 12, -5, -6, 50, 3], k = 3
    
    Return 15.667 // (-6 + 50 + 3) / 3 = 15.667

#### Basic Idea:
**Reference: ** **[Hyoung's Blog](http://blog.hyoung.me/cn/2017/02/binary-search-ii/)**

首先有一点需要明确，即如果当前子序列的最后一个元素为nums[i], 其之前小于0的最小前缀和为 min_pre，则该子序列的包含`nums[i]`的最大和为 `sum[nums[0:i]] - min_pre`。这也是 Kadane 算法的基本原理。

接下来，我们可以用二分法确定候选最大平均值，然后检验当前候选平均值的长度不小于k的子数组是否存在。这是二分结果的思路。

**具体地**，如何检验当前候选平均值是否存在呢。我们维持一个sums累加数组（sums[i]存放的是sum[nums[0:i]]），但是累加的不是原来的nums[i]而是`nums[i] - 候选avg`，这样我们只要找是否有长度不小于k且和大于等于0的子数组。**如何找到长度不小于K的呢？**我们只要限制 min_pre 为 i - k 之前的最小前缀和即可。

#### Python Code:
```python
    class Solution:
        # @param {int[]} nums an array with positive and negative numbers
        # @param {int} k an integer
        # @return {double} the maximum average
        def maxAverage(self, nums, k):
            # 检查长度 >= k 平均值 >= q 的subArray 是否存在
            def isValid(nums, k, target):
                sums = [0 for i in range(len(nums) + 1)]
                min_pre = 0
                for i in range(1, len(nums) + 1):
                # target 即为候选avg，累加数组sums
                    sums[i] = sums[i - 1] + nums[i - 1] - target
                    if i >= k:
                    # i-k 保证了最终结果是长度不小于k的子数组
                        min_pre = min(min_pre, sums[i - k])
                        if sums[i] - min_pre >= 0:
                            return True
                return False
            
            
            # use min and max as bounds to do binary search
            p = min(nums)
            r = max(nums)
            while r - p > 1e-6:
                q = (p + r) / 2.0
                if isValid(nums, k, q):
                    p = q
                else:
                    r = q
            return p
```