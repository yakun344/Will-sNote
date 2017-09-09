## Max Consecutive Ones II
_update Sep 9,2017  15:39_

---
[LeetCode](https://leetcode.com/problems/max-consecutive-ones-ii/description/)

Given a binary array, find the maximum number of consecutive 1s in this array if you can flip at most one 0.

**Example 1:**

    Input: [1,0,1,1,0]
    Output: 4
    Explanation: Flip the first zero will get the the maximum number of consecutive 1s.
    After flipping, the maximum number of consecutive 1s is 4.
**Note:**

The input array will only contain 0 and 1.  
The length of input array is a positive integer and will not exceed 10,000

**Follow up:**

What if the input numbers come in one by one as an infinite stream? In other words, you can't store all numbers coming from the stream as it's too large to hold in memory. Could you solve it efficiently?

#### Basic Idea:
**思路 1：sliding window**
维持一个window，保持window内只有至多一个0，返回最大的window size；

Java Code:
```java
    class Solution {
        public int findMaxConsecutiveOnes(int[] nums) {
            if (nums == null || nums.length == 0) return 0;
            int left = 0, right = 0;
            int count = 0; // window 中 0 的个数
            int maxSize = 1;
            while (right < nums.length) {
                // right 每次自动右移，依照当前window内0的个数，移动left
                if (nums[right] == 0) {
                    while (count > 0 && nums[left] != 0 && left < right) {
                        left++;
                    }
                    if (nums[left] == 0 && count == 1) left++;
                    count = 1;
                }
                maxSize = Math.max(maxSize, right - left + 1);
                right++;
            }
            return maxSize;
        }
    }
```

**思路 2：考虑 follow up**
利用 O(1) space, O(n) time。 基本思想就是计算每两段相隔一个0的连续1序列的最大长度和+1；
维持三个变量 a, b, c；
-  **a**： 当前之前的最优解；
-  **b**： 0 之前最长连续 1 的长度；
-  **c**： 0 之后最长连续 1 的长度；

维持这三个变量，每次循环后计算 `max(a, b + 1 + c)`；

Java Code:
```java







