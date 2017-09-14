## Number of Longest Increasing Subsequence
_update Sep 13,2017  23:09_

---
[LeetCode](https://leetcode.com/problems/number-of-longest-increasing-subsequence/description/)

Given an unsorted array of integers, find the number of longest increasing subsequence.

**Example 1:**

    Input: [1,3,5,4,7]
    Output: 2
Explanation: The two longest increasing subsequence are [1, 3, 4, 7] and [1, 3, 5, 7].

**Example 2:**

    Input: [2,2,2,2,2]
    Output: 5
Explanation: The length of longest continuous increasing subsequence is 1, and there are 5 subsequences' length is 1, so output 5.

**Note:**   
Length of the given array will be not exceed 2000 and the answer is guaranteed to be fit in 32-bit signed int.

#### Basic Idea:
基本思想和 LIS 类似，只是需要在之前记录以每个元素结尾的最长子序列的数组之外，再配一个储存以每个元素结尾的最长子序列个数的数组。如此一来，最终的解就是最终 LIS 结尾元素所对应的个数）。

此题实现起来不容易，有很多细节。

#### Java Code:
```java
    // O(n^2) solution
    class Solution {
        public int findNumberOfLIS(int[] nums) {
            if (nums == null || nums.length == 0) return 0;
            int[] len = new int[nums.length];
            int[] cnt = new int[nums.length];
            len[0] = 1;
            cnt[0] = 1;
            
            for (int i = 1; i < nums.length; ++i) {
                int max = 0, count = 1;
                for (int j = 0; j < i; ++j) {
                    if (nums[j] < nums[i]) {
                        if (max < len[j]) {
                            count = cnt[j];
                            max = len[j];
                        } else if (max == len[j]) {
                            count += cnt[j];
                        }
                    }
                }
                len[i] = max + 1;
                cnt[i] = count;
            }
            
            int max = 0, ret = 0;
            for (int i = 0; i < nums.length; ++i) {
                if (len[i] > max) {
                    max = len[i];
                    ret = cnt[i];
                } else if (len[i] == max) {
                    ret += cnt[i];
                }
            }
            if (ret == 0) return nums.length;
            return ret;
        }
    }
```

#### Python Code:
```python
    class Solution(object):
        def findNumberOfLIS(self, nums):
            """
            :type nums: List[int]
            :rtype: int
            """
            if not nums:
                return 0
            
            LIS = [1] + [0] * (len(nums) - 1)
            cnt = [1] + [0] * (len(nums) - 1)
            
            for i in range(1, len(nums)):
                maxLen, count = 0, 0
                for j in range(i):
                    if nums[j] < nums[i]:
                        if maxLen < LIS[j]:
                            maxLen = LIS[j]
                            count = cnt[j]
                        elif maxLen == LIS[j]:
                            count += cnt[j]
                if count == 0: count = 1
                LIS[i] = maxLen + 1
                cnt[i] = count
                
            maxLen, ret = 1, 0
            for i in range(len(nums)):
                if LIS[i] > maxLen:
                    maxLen = LIS[i]
                    ret = cnt[i]
                elif LIS[i] == maxLen:
                    ret += cnt[i]
            return ret
```