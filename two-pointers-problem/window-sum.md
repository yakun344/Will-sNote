## Window Sum
_update Aug 20,2017  17:06_

---
[LintCode](http://www.lintcode.com/en/problem/window-sum/)

Given an array of n integer, and a moving window(size k), move the window at each iteration from the start of the array, find the sum of the element inside the window at each moving.

**Example**

    For array [1,2,7,8,5], moving window size k = 3. 
    
    1 + 2 + 7 = 10
    2 + 7 + 8 = 17
    7 + 8 + 5 = 20
    
    return [10,17,20]
    
#### Basic Idea:
这道题目就是要实现一个最简单的 sliding window，贴在这里的主要目的是想日后或许可以优化这时候写的 sliding window 的code;

#### Python Code:
```python
    class Solution:
        # @param nums {int[]} a list of integers
        # @param k {int} size of window
        # @return {int[]} the sum of element inside the window at each moving
        def winSum(self, nums, k):
            left, right = 0, 0
            res = []
            suum = 0
            while right < len(nums):
                suum += nums[right]
                if right >= k - 1:
                    res.append(suum)
                    suum -= nums[left]
                    left += 1
                right += 1
            return res
```
            
            