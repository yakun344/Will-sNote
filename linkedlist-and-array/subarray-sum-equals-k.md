## Subarray Sum Equals K
_update Aug 13, 2017 18:00_

---
[LeetCode](https://leetcode.com/problems/subarray-sum-equals-k/description/)

Given an array of integers and an integer k, you need to find the total number of continuous subarrays whose sum equals to k.

**Example 1:**

    Input:nums = [1,1,1], k = 2
    Output: 2

**Note:**
The length of the array is in range [1, 20,000].
The range of numbers in the array is [-1000, 1000] and the range of the integer k is [-1e7, 1e7].

#### Basic Idea:
利用前缀和数组的特性，保存之前算出的前缀和。每到一个新的位置，例如当前前缀和为 sums[i], 如果之前有过前缀和等于 `sums[i] - k`, 则当前前缀和该前缀和只差为k，符合要求。

所以我们只要用一个counting map记录之前每个前缀和出现的数量，然后对每个新的前缀和，累加 `count += map[sums[i] - k]`，最终 count 就是解。**另外，**要注意初始的时候要 `map[0] = 1`, 否则无法统计到长度为 1 的满足要求的sequence （即num本身等于k）。

 #### Python Code:
 ```python
     class Solution(object):
        def subarraySum(self, nums, k):
            """
            :type nums: List[int]
            :type k: int
            :rtype: int
            """
            sums = [0] * (len(nums) + 1)
            prev = collections.defaultdict(int)
            prev[0] = 1
            count = 0
            for i in range(1, len(sums)):
                sums[i] = sums[i - 1] + nums[i - 1]
                count += prev[sums[i] - k]
                prev[sums[i]] += 1
            return count
 ``` 