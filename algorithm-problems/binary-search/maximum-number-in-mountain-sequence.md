# Maximum Number in Mountain Sequence

_update Aug 17,2017 0:24_

[LintCode](http://www.lintcode.com/en/problem/maximum-number-in-mountain-sequence/)

Given a mountain sequence of n integers which increase firstly and then decrease, find the mountain top.

**Example**

Given nums = \[1, 2, 4, 8, 6, 3\] return 8 Given nums = \[10, 9, 8, 7\], return 10

### Basic Idea:

用二分法逐渐像峰顶逼近，找拐点。

### Java Code：

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

### Python Code:

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

_update Dec 3, 2017 15:08_

## Update

重写这道题的时候，起初我的判断条件分三种情况，即 **当前是拐点，当前上升，当前下降** ，这样会使 code 变得冗长，看了之前写的 code 才恍然大悟，于是在这里做些补充。

事实上，在二分的过程中我们只需要跟踪第一个开始下降的点\(_之所以是下降起始点，是因为 q=\(p+r\)/2 的操作是偏左的，这样验证当前点和其右是否下降时不会越界_\)，如果退出时 p r 都不是下降起始点，则说明 nums 是一路上升，返回 nums\[-1\] 即可；

**java**

```java
    public class Solution {
        /*
         * @param nums: a mountain sequence which increase firstly and then decrease
         * @return: then mountain top
         */
        public int mountainSequence(int[] nums) {
            if (nums.length == 1) return nums[0];
            int p = 0, r = nums.length - 1;
            while (p + 1 < r) {
                int q = p + (r - p) / 2;
                int diff = nums[q + 1] - nums[q];
                if (diff <= 0) r = q;
                else p = q;
            }
            // 如果左边点p是下降起始，它就是解，如果它不是，则有可能是r，也有可能
            // r 是最右边的点，这两种情况都返回 r
            if (nums[p + 1] - nums[p] < 0) return nums[p];
            else return nums[r];
        }
    }
```

