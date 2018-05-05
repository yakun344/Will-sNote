## Merge Sorted Array
_update May 5,2018  14:37_

---
[LeetCode](https://leetcode.com/problems/merge-sorted-array/description/)

Given two sorted integer arrays nums1 and nums2, merge nums2 into nums1 as one sorted array.

**Note:**

The number of elements initialized in nums1 and nums2 are m and n respectively.
You may assume that nums1 has enough space (size that is greater or equal to m + n) to hold additional elements from nums2.

**Example:**

    Input:
    nums1 = [1,2,3,0,0,0], m = 3
    nums2 = [2,5,6],       n = 3
    
    Output: [1,2,2,3,5,6]
    
### Basic Idea
常规的做法可以先新建一个 aux 数组，将 nums1 的内容复制进去，然后合并 aux 和 nums2 到 nums1 中。但是这道题还有 `O(1)` space 的解法，就用从右向左扫描 nums1 和 nums2，每次将当前最大的数依次放在 nums1 右端。

下面提供 `O(1)` space 的解法。

* #### C++ Code：
```cpp
