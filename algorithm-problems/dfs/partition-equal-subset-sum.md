# Partition Equal Subset Sum

_update 2018-10-10 18:45:46_

[LeetCode](https://leetcode.com/problems/partition-equal-subset-sum/description/)

Given a non-empty array containing only positive integers, find if the array can be partitioned into two subsets such that the sum of elements in both subsets is equal.

**Note:**

1. Each of the array element will not exceed 100.
2. The array size will not exceed 200.

**Example 1:**

```text
Input: [1, 5, 11, 5]

Output: true

Explanation: The array can be partitioned as [1, 5, 5] and [11].
```

**Example 2:**

```text
Input: [1, 2, 3, 5]

Output: false

Explanation: The array cannot be partitioned into equal sum subsets.
```

## Basic Idea:

首先可以通过 `sum/2` 来求得 targetSum，如果这个除不尽可以直接返回 false。基本的思想还是 DFS，用两个变量 sum1 和 sum2 分别表示第一个 set 和第二个 set 的 sum，每层考虑一个 index的 num 应该放入哪个 set，共有 `len(nums)` 层。时间复杂度为 `O(2^len(nums))`。

**优化**：

* 如果在一层 dfs 中发现 sum1 和 sum2 的值相等，只需要考虑其中之一就可以了；
* 可以先将 nums 排序，然后从大到小考虑每个数字，这样可以更早发现此路不通的情况；

### Java Code:

```java
class Solution {
    public boolean canPartition(int[] nums) {
        if (nums.length < 2) return false;
        int sum = 0;
        for (int num : nums) sum += num;
        if ((sum & 1) != 0) return false;
        int targetSum = sum / 2;
        Arrays.sort(nums);
        return helper(nums, nums.length - 1, 0, 0, targetSum);
    }

    private boolean helper(int[] nums, int pos, int sum1, int sum2, int targetSum) {
        if (pos == -1) return true;
        if (sum1 + nums[pos] <= targetSum) {
            if (helper(nums, pos - 1, sum1 + nums[pos], sum2, targetSum)) return true;
        }
        if (sum1 != sum2 && sum2 + nums[pos] <= targetSum) {
            if (helper(nums, pos - 1, sum1, sum2 + nums[pos], targetSum)) return true;
        }
        return false;
    }
}
```

