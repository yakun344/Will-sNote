## Combination Sum IV
_update Aug 15,2017  18:46_

---
[LeetCode](https://leetcode.com/problems/combination-sum-iv/description/)


Given an integer array with all positive numbers and no duplicates, find the number of possible combinations that add up to a positive integer target.

**Example:**

    nums = [1, 2, 3]
    target = 4

The possible combination ways are:
(1, 1, 1, 1)
(1, 1, 2)
(1, 2, 1)
(1, 3)
(2, 1, 1)
(2, 2)
(3, 1)

**Note** that different sequences are counted as different combinations.

Therefore the output is 7.

**Follow up:**
*  What if negative numbers are allowed in the given array?
*  How does it change the problem?
*  What limitation we need to add to the question to allow negative numbers?

#### Basic Idea:
虽然叫Combination，但由于事实上可以重复的数字以不同顺序出现在答案中，实际上这是道Permutation的问题。由于每个数字都可以使用不止一次，我们不需要使用used数组，只要无脑dfs即可（在每层dfs中我们都可以选择nums中的任意数字）。由于可能出现重复（不同路径，但是剩余sum相同），我们可以使用一个HashMap存放`<remainSum，#result>`，进行 Memoized Searching。

**Follow Up** 的解法：
由于允许有负数出现，答案就有可能是无穷多个（就像有 negative circle 的 graph 中找固定之外距离的node）。所以，这种情况下，题目就需要限制答案的长度，固定`答案长度 <= length`。

#### Python Code:
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



