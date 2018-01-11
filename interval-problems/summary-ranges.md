## Summary Ranges
_update Aug 12, 2017  13:02_

---
[LeetCode](https://leetcode.com/problems/summary-ranges/description/)


Given a sorted integer array without duplicates, return the summary of its ranges.

**For example,** 
given [0,1,2,4,5,7], return ["0->2","4->5","7"].

#### Basic Idea:
和 interval 的题目类似，维持一个currStart，和一个 currEnd，从左到右更新。

#### Python Code:
    ```python
    class Solution(object):
        def summaryRanges(self, nums):
            """
            :type nums: List[int]
            :rtype: List[str]
            """
            res = []
            if not nums: return res
            currStart = nums[0]
            currEnd = nums[0]
            # 如果curr和end只差1，则更新end，否则加入结果
            for i in range(1, len(nums)):
                num = nums[i]
                if num == currEnd + 1:
                    currEnd = num
                else:
                    if currStart == currEnd:
                        res.append(str(currStart))
                    else:
                        res.append(str(currStart) + '->' + str(currEnd))
                    currStart = num
                    currEnd = num
            if currStart == currEnd:
                res.append(str(currStart))
            else:
                res.append(str(currStart) + '->' + str(currEnd))
            return res
```