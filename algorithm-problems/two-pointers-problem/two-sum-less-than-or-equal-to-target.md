# Two Sum - Less than or equal to target

_Update Aug 19,2017 16:51_

[LintCode](http://www.lintcode.com/en/problem/two-sum-less-than-or-equal-to-target/)

Given an array of integers, find how many pairs in the array such that their sum is less than or equal to a specific target number. Please return the number of pairs.

**Example**

```text
Given nums = [2, 7, 11, 15], target = 24. 
Return 5. 
2 + 7 < 24
2 + 11 < 24
2 + 15 < 24
7 + 11 < 24
7 + 15 < 25
```

## Basic Idea:

利用双指针算法，先固定第一个数（小数），然后找满足条件的最大的第二个数的位置；

两个指针从最左和最右开始，如果 `nums[left] + nums[right] <= target` ，说明left往右到right的所有数和 nums\[left\] 相加都满足要求，则 `count+=right-left` ，此时我们已经找到了以 nums\[left\] 为第一个数的所有第二个数的个数，然后我们令 left++，开始考虑下一个数字作为第一个数的情况。

这里需要注意的是，必选对于每个小数考虑符合条件的大数的个数，因为left和right之间的数字都是比right小的，确定了right的位置就可以知道这些数字的个数。

**拓展：**如果问题变为找**大于等于target**的pair个数，我们就需要以每个大数作为第一个数，寻找满足条件的最小数字的位置，等于之前的过程左右相反了。

## Java Code:

```java
    public class Solution {
        /**
         * @param nums an array of integer
         * @param target an integer
         * @return an integer
         */
        public int twoSum5(int[] nums, int target) {
            Arrays.sort(nums);
            int left = 0, right = nums.length - 1;
            int ret = 0;
            while (left < right) {
                if (nums[left] + nums[right] <= target) {
                    ret += right - left;
                    left++;
                } else {
                    right--;
                }
            }
            return ret;
        }
    }
```

