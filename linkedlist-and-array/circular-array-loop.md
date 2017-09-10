## Circular Array Loop
_update Sep 10, 2017  13:28_

---
[LeetCode](https://leetcode.com/problems/circular-array-loop/description/)

You are given an array of positive and negative integers. If a number n at an index is positive, then move forward n steps. Conversely, if it's negative (-n), move backward n steps. Assume the first element of the array is forward next to the last element, and the last element is backward next to the first element. Determine if there is a loop in this array. A loop starts and ends at a particular index with more than 1 element along the loop. The loop must be "forward" or "backward'.

**Example 1**:   
Given the array [2, -1, 1, 2, 2], there is a loop, from index 0 -> 2 -> 3 -> 0.

**Example 2**:   
Given the array [-1, 2], there is no loop.

**Note:** 
The given array is guaranteed to contain no element "0".   
Can you do it in O(n) time complexity and O(1) space complexity?

#### Basic Idea:
**思路 1：slow-fast**
用两个指针，一个slow一个fast，分别对正数和负数进行检查，相遇就有环。但是要另外记得处理环中只有一个元素的情况；  

Java Code:
```java
    class Solution {
        public boolean circularArrayLoop(int[] nums) {
            int len = nums.length;
            int positive = -1, negative = -1;
            // 对每个数字，检查circle，分为正数负数两种情况考虑
            for (int i = 0; i < len; ++i) {
                if (nums[i] > 0) {
                    if (checkCircle(nums, i)) return true;
                } else if (nums[i] < 0) {
                    if (checkCircle(nums, i)) return true;
                }
            }
            return false;
        }
        private boolean checkCircle(int[] nums, int start) {
            int len = nums.length;
            boolean positive = false;
            if (nums[start] > 0) positive = true;
            else positive = false;
            
            int slow = start, fast = start;
            while ((nums[slow] > 0) == positive && 
                   (nums[fast] > 0) == positive &&
                   (nums[getIndex(fast + nums[fast], len)] > 0) == positive) {
                slow = getIndex(slow + nums[slow], len);
                if (getIndex(slow + nums[slow], len) == slow) break; // 检查环中只有一个元素的情况
                fast = getIndex(fast + nums[fast], len); // 前进两步
                fast = getIndex(fast + nums[fast], len);
                if (nums[slow] == nums[fast]) return true;
            }
            return false;
        }
        private int getIndex(int input, int len) {
            if (input >= 0) return input % len;
            return len - (len - input) % len;
        }
    }
```















