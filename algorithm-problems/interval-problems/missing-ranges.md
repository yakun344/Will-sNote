# Missing Ranges

_update Aug 12, 2017 13:34_

[LeetCode](https://leetcode.com/problems/missing-ranges/description/)

Given a sorted integer array where the range of elements are in the inclusive range \[lower, upper\], return its missing ranges.

**For example**

```text
given [0, 1, 3, 50, 75], lower = 0 and upper = 99, 
return ["2", "4->49", "51->74", "76->99"].
```

## Basic Idea:

维持一个curr，从lower-1向右扫描。如果 num==curr+1 ,则更新curr=num，否则把\[curr+1, num-1\]加入res，更新curr=num。

有一个技巧：在原nums中append upper+1 可以简便操作，直接得到结果。

## Java Code:

```java
    class Solution(object):
        def findMissingRanges(self, nums, lower, upper):
            """
            :type nums: List[int]
            :type lower: int
            :type upper: int
            :rtype: List[str]
            """
            nums.append(upper + 1)
            res = []
            curr = lower - 1
            for n in nums:
                if n <= curr + 1:
                    curr = n
                else:
                    left = curr + 1
                    right = n - 1
                    if left == right: res.append(str(left))
                    else: res.append(str(left) + '->' + str(right))
                    curr = n
            return res
```

