# Search in Rotated Sorted Array

_update Sep 10, 2017 15:00_

[LeetCode](https://leetcode.com/problems/search-in-rotated-sorted-array/description/)

Suppose an array sorted in ascending order is rotated at some pivot unknown to you beforehand.

\(i.e., 0 1 2 4 5 6 7 might become 4 5 6 7 0 1 2\).

You are given a target value to search. If found in the array return its index, otherwise return -1.

You may assume no duplicate exists in the array.

## Basic Idea:

传说中的最难典型二分法题目，但其实搞懂了套路也可以轻易做出来。破题点是要把一个 rotated sorted array 想象成如下图的样子：  
  
 ![](../../.gitbook/assets/screen-shot-2017-09-10-at-3.05.50-pm%20%281%29.png)   


如此一来，我们只要按照 target 在前半部分还是后半部分进行分类讨论，然后就可以写出合适的binary search。

**Follow Up:**  
What if duplicates are allowed?

> 如果重复允许出现，会有如下情况：`nums: [1,1,1,1,3,1,1,1], target=3` 这种情况下，只能是用O\(n\)的解法，我们没有办法做得更好。

## Java Code:

```java
    class Solution {
        public int search(int[] nums, int target) {
            if (nums == null || nums.length == 0) return -1;
            int p = 0, r = nums.length - 1;
            int last = nums[nums.length - 1];
            while (p + 1 < r) {
                int q = (r - p) / 2 + p;
                if (target > last) {
                    if(nums[q] < last) 
                        r = q;
                    else if (nums[q] < target) 
                        p = q;
                    else
                        r = q;
                } else {
                    if (nums[q] > last) 
                        p = q;
                    else if (nums[q] < target) 
                        p = q;
                    else
                        r = q;
                }
            }
            if (nums[p] == target) return p;
            if (nums[r] == target) return r;
            return -1;
        }
    }
```

_update Dec 17, 2017 1:14_

## Python Code

```python
    class Solution:
        def search(self, nums, target):
            """
            :type nums: List[int]
            :type target: int
            :rtype: int
            """
            if not nums: return -1
            p, r = 0, len(nums) - 1
            while p + 1 < r:
                q = p + (r - p) // 2
                if target > nums[-1]:
                    if nums[q] < nums[-1] or nums[q] > target: r = q
                    else: p = q
                else:
                    if nums[q] > nums[-1] or nums[q] < target: p = q
                    else: r = q
            if nums[p] == target: return p
            elif nums[r] == target: return r
            else: return -1
```

