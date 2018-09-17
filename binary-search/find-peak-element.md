## Find Peak Element
_update Aug 17,2017  1:48_

---
[LintCode](http://www.lintcode.com/en/problem/find-peak-element/)
[LeetCode](https://leetcode.com/problems/find-peak-element/description/)

There is an integer array which has the following features:

The numbers in adjacent positions are different.
`A[0] < A[1] && A[A.length - 2] > A[A.length - 1]`.
We define a position P is a peek if:

`A[P] > A[P-1] && A[P] > A[P+1]`
Find a peak element in this array. Return the index of the peak.

**Notice**

The array may contains multiple peeks, find any of them.

#### Basic Idea:
在给定数组中找比左右邻居大的数。而且这个数组一定是先升后降，中间可能会有多个拐点，相邻的数字都不同。

解题思路是利用二分法，如果q在上升中，就左边逼近，如果在下降，就右边逼近，这样一定可以找到一个峰。

#### Java Code：
```java
    class Solution {
        /**
         * @param A: An integers array.
         * @return: return any of peek positions.
         */
        public int findPeak(int[] A) {
            // write your code here
            if (A == null || A.length < 3) return -1;
            int p = 0, r = A.length - 1;
            while (p + 1 < r) {
                int q = p + (r - p) / 2;
                if (A[q] - A[q - 1] > 0) p = q;
                if (A[q + 1] - A[q] < 0) r = q;
                else p = q;
            }
            return A[p] > A[r] ? p : r;
        }
    }
```

---
_update Sep 16 2018, 16:53_

### Update: LeetCode 版本
LeetCode上面的版本不保证最左和最右一定不是解，也不保证input array的size是大于等于3的，所以在二分法之前需要进行一些特判。

* #### Java Code:
  ```Java
  class Solution {
    public int findPeakElement(int[] nums) {
        if (nums.length == 1) return 0;
        else if (nums[0] > nums[1]) return 0;
        else if (nums[nums.length - 1] > nums[nums.length - 2]) return nums.length - 1;
        int left = 1, right = nums.length - 2;
        while (left + 1 < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid - 1] < nums[mid] && nums[mid] < nums[mid + 1]) {
                left = mid;
            } else {
                right = mid;
            }
        }
        if (nums[left] > nums[left - 1] && nums[left] > nums[left + 1]) return left;
        else return right;
    }
  }
  ```
