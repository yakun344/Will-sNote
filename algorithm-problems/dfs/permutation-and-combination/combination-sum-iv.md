# Combination Sum IV

_update Aug 15,2017 18:46_

[LeetCode](https://leetcode.com/problems/combination-sum-iv/description/)

Given an integer array with all positive numbers and no duplicates, find the number of possible combinations that add up to a positive integer target.

**Example:**

```text
nums = [1, 2, 3]
target = 4
```

The possible combination ways are: \(1, 1, 1, 1\) \(1, 1, 2\) \(1, 2, 1\) \(1, 3\) \(2, 1, 1\) \(2, 2\) \(3, 1\)

**Note** that different sequences are counted as different combinations.

Therefore the output is 7.

**Follow up:**

* What if negative numbers are allowed in the given array?
* How does it change the problem?
* What limitation we need to add to the question to allow negative numbers?

## Basic Idea:

虽然叫Combination，但由于事实上可以重复的数字以不同顺序出现在答案中，实际上这是道Permutation的问题。由于每个数字都可以使用不止一次，我们不需要使用used数组，只要无脑dfs即可（在每层dfs中我们都可以选择nums中的任意数字）。由于可能出现重复（不同路径，但是剩余sum相同），我们可以使用一个HashMap存放`<remainSum，#result>`，进行 Memoized Searching。

**Follow Up** 的解法： 由于允许有负数出现，答案就有可能是无穷多个（就像有 negative circle 的 graph 中找固定之外距离的node）。所以，这种情况下，题目就需要限制答案的长度，固定`答案长度 <= length`。

## Python Code:

```python
    class Solution(object):
        def combinationSum4(self, nums, target):
            """
            :type nums: List[int]
            :type target: int
            :rtype: int
            """
            def dfs(nums, remainSum):
                if remainSum < 0:
                    return 0
                if remainSum == 0:
                    return 1
                if remainSum in dp:
                    return dp[remainSum]
                ret = 0
                for n in nums:
                    ret += dfs(nums, remainSum - n)
                dp[remainSum] = ret
                return ret

            dp = {}
            return dfs(nums, target)
```

_update 2018-07-25 22:14:52_

## Update：DP，bottom-up solution

我们可以用DP的思路来做，首先我们有如下状态转移：

```java
  dp[target] = sum{ dp[target - num] } for num in nums;
  base case: dp[0] = 1;
```

于是，我们只要i从 1 开始向右直到 target，每次检查nums中每个数字，如果`target-num >= 0`，生成i的组合个数就加上 `dp[target - num]`；

```java
class Solution {
    public int combinationSum4(int[] nums, int target) {
        int[] dp = new int[target + 1];
        dp[0] = 1;
        for (int i = 0; i < dp.length; ++i) {
            for (int num : nums) {
                if (i - num >= 0) dp[i] += dp[i - num];
            }
        }
        return dp[target];
    }
}
```

