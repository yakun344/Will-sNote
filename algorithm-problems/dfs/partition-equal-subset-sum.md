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

### 1. DFS

首先可以通过 `sum/2` 来求得 targetSum，如果这个除不尽可以直接返回 false。基本的思想还是 DFS，用两个变量 sum1 和 sum2 分别表示第一个 set 和第二个 set 的 sum，每层考虑一个 index的 num 应该放入哪个 set，共有 `len(nums)` 层。时间复杂度为 `O(2^len(nums))`。

**优化**：

* 如果在一层 dfs 中发现 sum1 和 sum2 的值相等，只需要考虑其中之一就可以了；
* 可以先将 nums 排序，然后从大到小考虑每个数字，这样可以更早发现此路不通的情况；

### 2. DP

新建boolean dp数组长度为`target+1`，其中存的是能否在数组中找到k个数，使得他们的和等于 i。则如果最终 `dp[target] == true`，则说明可以找到一本数字使他们的和等于 target，那么剩下的另一部分的和一定也是target，于是只需要返回 `dp[target]` 即可。

至于如何更新dp数组，我们使用两层for loop，外层循环每个num，内层则是考虑每个 i 的值，如果 `dp[i - num] == true`, 则可以将`dp[i]` 设为 true。这种方法实际上考虑了数组中数字能组成的所有和的可能，时间复杂度为 `O(range(nums) * len(nums))`。

需要注意，实现的时候内层循环必须从右向左，否则之前更新过的 `dp[i+num]` 会影响后面的结果。

## Java Code:

### 1. DFS

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

### 2. DP

```java
class Solution {
    public boolean canPartition(int[] nums) {
        int sum = 0;
        for (int num : nums) sum += num;
        if (sum % 2 != 0) return false;
        int target = sum / 2;
        
        boolean[][] dp = new boolean[nums.length + 1][target + 1];
        dp[0][0] = true;
        for (int i = 1; i < nums.length + 1; ++i) {
            for (int j = 0; j <= target; ++j) {
                if (j - nums[i - 1] >= 0 && dp[i - 1][j - nums[i - 1]]) {
                    dp[i][j] = true;
                } else if (dp[i - 1][j]) {
                    dp[i][j] = true;
                } 
            }
        }
        return dp[nums.length][target];
    }
}

// 优化过空间的DP
class Solution {
    public boolean canPartition(int[] nums) {
        int sum = 0;
        for (int num : nums) {
            sum += num;
        }
        if (!(sum % 2 == 0)) {
            return false;
        }
        int target = sum / 2;
        boolean[] dp = new boolean[target + 1];
        dp[0] = true;
        for (int num : nums) {
            for (int i = target - num; i >= 0; --i) {
                if (dp[i]) {
                    dp[i + num] = true;
                }
            }
        }
        return dp[target];
    }
}
```

