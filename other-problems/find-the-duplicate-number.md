## Find the Duplicate Number
_udpate Agu 6,2017  19:57_

---
[LeetCode](https://leetcode.com/problems/find-the-duplicate-number/description/)

Given an array nums containing n + 1 integers where each integer is between 1 and n (inclusive), prove that at least one duplicate number must exist. Assume that there is only one duplicate number, find the duplicate one.

**Note:**

    You must not modify the array (assume the array is read only).
    You must use only constant, O(1) extra space.
    Your runtime complexity should be less than O(n2).
    There is only one duplicate number in the array, but it could be repeated more than once.
    
#### Basic Idea:
**思路1：**Binary Search
从 p = 0, r = n 开始，每次计算小于等于 mid 的数的个数，如果大于mid，则说明重复在左侧，否则在右侧。
```python
    # binary search solution O(nlogn) time
    class Solution(object):
        def findDuplicate(self, nums):
            """
            :type nums: List[int]
            :rtype: int
            """
            p = 1
            r = len(nums) - 1
            while p + 1 < r:
                q = (r - p) / 2 + p
                count = len([n for n in nums if n <= q])
                if count <= q: p = q
                else: r = q
            if len([n for n in nums if n <= p]) > p:
                return p
            else:
                return r
```

**思路2：**判断链表环的位置
[参考文章](https://www.hrwhisper.me/leetcode-find-the-duplicate-number/) 这里有一个不错的分析。
**摘要：**
如果数组中元素不重复，那么,任意下标i和数组中该下标的值一一对应，如 对于数组 3,4,1,2，有如下对应关系：（注意，值从1~n）

    0 – > 2
    1  -> 4
    2  -> 1
    3  -> 3

设这个对应关系的运算函数为f(n) ，那么，我们从下标0出发，每次根据f取出下标i对应的值x，并将这个x作为下一次的下标，直到下标越界。
如3，4，1，2这个数组，那么有 0 – > 2-> 1-> 4

但如果有重复的话，中间就会产生多个映射，如3,4,1,2,3

    0 – > 2
    1  -> 4
    2  -> 1
    3  -> 3
    4  ->3

继续这么做的话，会发生 0 – > 2-> 1-> 4  -> 3 -> 3->3……
也就是最后一定是那个重复的元素。

```java
    public class Solution {
        public int findDuplicate(int[] nums) {
            int slow = nums[0];
            int fast = nums[nums[0]];
            do {
                slow = nums[slow];
                fast = nums[nums[fast]];
            } while (slow != fast);
            int ret = 0;
            while (ret != slow) {
                slow = nums[slow];
                ret = nums[ret];
            }
            return ret;
        }
    }
```