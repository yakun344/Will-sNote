## Remove Duplicates from Sorted Array
_update Jun 28, 2017_

---

[leetcode](https://leetcode.com/problems/remove-duplicates-from-sorted-array/#/description)

Given a sorted array, remove the duplicates in place such that each element appear only once and return the new length.

Do not allocate extra space for another array, you must do this in place with constant memory.

**For example,**

    Given input array nums = [1,1,2],

Your function should return `length = 2`, with the first two elements of nums being 1 and 2 respectively. It doesn't matter what you leave beyond the new length.

##### 思路
首先我们的目标一定是在 O(n) 的时间内解决问题，类比 quick sort的partition，我们可以写出类似的 2 pointers 的算法。维持两个指针`i, j`，i 左边是我们的最终结果，j 右边是还没有探测的原数组，`i, j`之间是存在 duplicates 的部分。最终 i 会把数组一分为二，左边就是我们需要的部分。

##### Code
Python code：
```python
    class Solution(object):
        def removeDuplicates(self, nums):
            """
            :type nums: List[int]
            :rtype: int
            """
            if len(nums) == 0: return 0
            i = 0
            j = 0
            while j < len(nums):
                if nums[i] != nums[j]:
                    i += 1
                    nums[i] = nums[j]
                j += 1
            return i + 1
```

---
_update Nov 27, 2017  11:10_

#### 更新code
之前的写法，起初令 i,j 都等于0，依靠第一次 while 判定 nums[i]==nums[j] 不做操作。改进之后，令初始 i, j = 0, 1，这样更好理解：

```python
    class Solution(object):
        def removeDuplicates(self, nums):
            """
            :type nums: List[int]
            :rtype: int
            """
            if not nums:
                return 0
            left, right = 0, 1
            while right < len(nums):
                if nums[left] != nums[right]:
                    left += 1
                    nums[left] = nums[right]
                right += 1
            return left + 1
```






