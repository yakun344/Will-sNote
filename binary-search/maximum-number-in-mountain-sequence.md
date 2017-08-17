## Maximum Number in Mountain Sequence
_update Aug 17,2017  0:24_

---
[LintCode](http://www.lintcode.com/en/problem/maximum-number-in-mountain-sequence/)

Given a mountain sequence of n integers which increase firstly and then decrease, find the mountain top.

**Example**

Given nums = [1, 2, 4, 8, 6, 3] return 8
Given nums = [10, 9, 8, 7], return 10

#### Basic Idea:
用二分法逐渐像峰顶逼近，找拐点。

#### Java Code：
```java
    public class Solution {
        /**
         * @param nums a mountain sequence which increase firstly and then decrease
         * @return then mountain top
         */
        public int mountainSequence(int[] nums) {
            // Write your code here
            if (nums == null || nums.length < 1) return 0;
            if (nums.length == 1) return nums[0];
            int p = 0, r = nums.length - 1;
            while (p + 1 < r) {
                int q = (r - p) / 2 + p;
                if (nums[q + 1] - nums[q] > 0) p = q;
                if (nums[q + 1] - nums[q] < 0) r = q;
            }
            if (nums[p + 1] - nums[p] < 0) return nums[p];
            else return nums[r];
        }
    }
```

#### Python Code:
```python
    class Solution:
        # @param {int[]} nums a mountain sequence which increase firstly and then decrease
        # @return {int} then mountain top
        def mountainSequence(self, nums):
            if len(nums) == 1:
                return nums[0]
            p = 0
            r = len(nums) - 1
            while p + 1 < r:
                q = (p + r) / 2
                if nums[q + 1] - nums[q] > 0:
                    p = q
                else:
                    r = q
            if nums[p + 1] - nums[p] < 0:
                return nums[p]
            else:
                return nums[r]
```