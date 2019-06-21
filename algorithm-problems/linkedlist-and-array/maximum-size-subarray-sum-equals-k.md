# Maximum Size Subarray Sum Equals k

_update Sep 3,2018 23:12_

Given an array nums and a target value k, find the maximum length of a subarray that sums to k. If there isn't one, return 0 instead.

**Note:**

The sum of the entire nums array is guaranteed to fit within the 32-bit signed integer range.

**Example 1:**

```text
Input: nums = [1, -1, 5, -2, 3], k = 3
Output: 4
Explanation: The subarray [1, -1, 5, -2] sums to 3 and is the longest.
```

 **Example 2:**

```text
Input: nums = [-2, -1, 2, 1], k = 1
Output: 2
Explanation: The subarray [-1, 2] sums to 1 and is the longest.
```

**Follow Up:**

Can you do it in O\(n\) time?

## Basic Idea:

要求和为k的最长subarray的长度。基本思路是使用 prefixSum 数组加速求和过程。先生成 prefix sum 数组，然后从左往右扫描，用一个hashmap记录之前出现过的prefixSum的最左index，检查当前prefixSum的值，看之前是否又出现过符合条件的，若有，计算当前index和之前index的差，更新返回值为所有符合条件index差值的最大值。

## Java Code:

```java
  class Solution {
      public int maxSubArrayLen(int[] nums, int k) {
          Map<Integer, Integer> map = new HashMap<>();
          int[] prefixSum = new int[nums.length + 1];
          for (int i = 0; i < nums.length; ++i) {
              prefixSum[i + 1] = prefixSum[i] + nums[i];
          }
          int ret = 0;
          for (int i = 0; i < prefixSum.length; ++i) {
              if (map.containsKey(prefixSum[i] - k)) {
                  ret = Math.max(ret, i - map.get(prefixSum[i] - k));
              }
              if (! map.containsKey(prefixSum[i])) {
                  map.put(prefixSum[i], i);
              }
          }
          return ret;
      }
  }
```

