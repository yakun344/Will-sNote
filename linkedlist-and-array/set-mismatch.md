## Set Mismatch
_update Sep 17, 2017  11:13_

---
[LeetCode](https://leetcode.com/problems/set-mismatch/description/)

The set S originally contains numbers from 1 to n. But unfortunately, due to the data error, one of the numbers in the set got duplicated to another number in the set, which results in repetition of one number and loss of another number.

Given an array nums representing the data status of this set after the error. Your task is to firstly find the number occurs twice and then find the number that is missing. Return them in the form of an array.

**Example 1:**

    Input: nums = [1,2,2,4]
    Output: [2,3]
    
**Note:**

1. The given array size will in the range [2, 10000].
2. The given array's numbers won't have any order.

#### Basic Idea:
题目所求的是，在一个长度为 n 的应该含有 1-n 数字的数组中，有一个数字缺失，同时造成一个数字重复。要求找到重复的和缺失的数字。

**思路 1：HashSet**  

1.  使用一个 HashSet，先把 1-n 全部放进去，然后对每个 nums 中的数字，在set中删除，如果发现某个数字在删除之前就不在set中，则说明这个数字就是重复的数字。
2.  删除之后，因为有一个数字缺失，此时set中一定还有一个数字，这个就是缺失的数字。

**思路 2：用数组代替 set**
使用一个数组，长度为 n+1，则此时其index就可以代表 1-n 的数字。

**思路 3：O(1) space**
直接在原数组上操作，用正负号表示是否在set中。同时，因为原数组index是 0 -（n-1）的，我们用 index 来表示数 index+1。

#### Python Code：
```python
    # 方法 1：使用HashSet
    class Solution(object):
        def findErrorNums(self, nums):
            """
            :type nums: List[int]
            :rtype: List[int]
            """
            st = set([n for n in range(1, len(nums) + 1)])
            ret = [None] * 2
            for n in nums:
                if n in st:
                    st.remove(n)
                else:
                    ret[0] = n
            ret[1] = st.pop()
            return ret
    
    # 方法 2： 用数组代替 HashSet
    class Solution(object):
        def findErrorNums(self, nums):
            """
            :type nums: List[int]
            :rtype: List[int]
            """
            st = [n for n in range(len(nums) + 1)] # [0,1,2,3...,n]
            ret = [None] * 2
            
            for n in nums:
                if st[n] != 0:
                    st[n] = 0
                else:
                    ret[0] = n
                    
            ret[1] = [n for n in st if n != 0][0]
            return ret
    
    # 方法 3： O(1) space, 用原数组，abs表示值，正负表示 当前index+1 是否在 set 中
    class Solution(object):
        def findErrorNums(self, nums):
            """
            :type nums: List[int]
            :rtype: List[int]
            """
            ret = [None] * 2
            
            for n in nums:
                n = abs(n)
                if nums[n - 1] > 0:
                    nums[n - 1] *= -1
                else:
                    ret[0] = n
            
            ret[1] = [i + 1 for i in range(len(nums)) if nums[i] > 0][0]
            return ret
```







