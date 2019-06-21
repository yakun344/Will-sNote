# Partition Array II

_update Aug 21, 2017 16:10_

[LintCode](http://www.lintcode.com/en/problem/partition-array-ii/)

Partition an unsorted integer array into three parts:

* The front part &lt; low
* The middle part &gt;= low & &lt;= high
* The tail part &gt; high

Return any of the possible solutions.

**Notice**

`low <= high` in all testcases.

**Example**

```text
Given [4,3,4,1,2,3,1,2], and low = 2 and high = 3.
Change to [1,1,2,3,2,3,4,4].   
([1,1,2,2,3,3,4,4] is also a correct answer, but [1,2,1,2,3,3,4,4] is not)
```

**Challenge**

* Do it in place.
* Do it in one pass \(one loop\).

## Basic Idea:

和 sort Colors 的思路一样，使用left和right两个指针分别跟踪 `<low 和 >high` 元素的边界index，用另一个指针从左向右扫描。但要注意边界条件。

## Java Code:

```java
    public class Solution {
        /**
         * @param nums an integer array
         * @param low an integer
         * @param high an integer
         * @return nothing
         */
        public void partition2(int[] nums, int low, int high) {
            int left = 0, right = nums.length - 1, i = 0;
            while (i <= right && left <= right) {
                if (nums[i] < low) {
                    // 写这里的时候，要想到此时换过来的元素只有两种情况：
                    // 1. 此时 i==left，交换实际并无操作，之后left++，i++；
                    // 2. 此时 i>left，交换之后，i位置的数字之前其实已经被处理过（==low or high），
                    //    此时可以放心直接令left++，i++；
                    // 所以两种情况下的代码是一样的。
                    swap(nums, left++, i++);
                } else if (nums[i] > high) {
                    swap(nums, right--, i);
                } else {
                    i++;
                }
            }
        }
        private void swap(int[] nums, int i, int j) {
            int t = nums[i];
            nums[i] = nums[j];
            nums[j] = t;
        }
    }
```

