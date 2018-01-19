## Next Greater Element II
_update Jul 12, 2017 15:12_

---
[Leetcode](https://leetcode.com/problems/next-greater-element-ii/#/solutions)

Given a circular array (the next element of the last element is the first element of the array), print the Next Greater Number for every element. The Next Greater Number of a number x is the first greater number to its traversing-order next in the array, which means you could search circularly to find its next greater number. If it doesn't exist, output -1 for this number.

    Example 1:
    Input: [1,2,1]
    Output: [2,-1,2]
    Explanation: The first 1's next greater number is 2; 
    The number 2 can't find next greater number; 
    The second 1's next greater number needs to search circularly, which is also 2.
**Note:** The length of given array won't exceed 10000.

#### Basic Idea:
利用 `stack` 来加速，实现`O(n)`的复杂度。先把第一个元素的下标加入`stack`，从左往右查看，如果遇到比当前`peek`所对应元素大的元素，则当前`peek`所对应位置的`next greater element`为当前遇到的元素，`pop`之后再和新的`peek`比较。直到当前`element < nums[stack.peek()]`，把当前`element`的下标`push`。

#### Python Code:
```python
    class Solution(object):
        def nextGreaterElements(self, nums):
            """
            :type nums: List[int]
            :rtype: List[int]
            """
            res = [-1] * len(nums)
            nums = nums + nums[:]
            stack = [0]
            for i in range(len(nums)):
                while stack and nums[i] > nums[stack[-1]]:
                    idx = stack.pop()
                    if idx < len(res):
                        res[idx] = nums[i]
                if i < len(res):
                    stack.append(i)
            return res
```