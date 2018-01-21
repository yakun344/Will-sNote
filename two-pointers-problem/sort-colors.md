## Sort Colors
_update Aug 19,2017  23:36_

---
[LintCode](http://www.lintcode.com/en/problem/sort-colors/)
[LeetCode](https://leetcode.com/problems/sort-colors/description/)


Given an array with n objects colored red, white or blue, sort them so that objects of the same color are adjacent, with the colors in the order red, white and blue.

Here, we will use the integers 0, 1, and 2 to represent the color red, white, and blue respectively.

**Note:**
You are not suppose to use the library's sort function for this problem.


**Follow up:**
A rather straight forward solution is a two-pass algorithm using counting sort.
First, iterate the array counting number of 0's, 1's, and 2's, then overwrite array with total number of 0's, then 1's and followed by 2's.

Could you come up with an one-pass algorithm using only constant space?

<br>

#### Basic Idea:
[这里](http://bangbingsyb.blogspot.com/2014/11/leetcode-sort-colors.html) 有一个非常好的分析。

**思路1：**   
counting sort，这个比较简单；

**思路2：**   
维持左右两个指针，从最左端和最右开始，分表表示 0 的最右边和 2 的最左边位置。用另一个指针扫描数组，然后做相应的交换。下面的分析摘自上面ref的文章：

假设已经完成到如下所示的状态：
```

              0......0   1......1  x1 x2 .... xm    2.....2
                                    |         |     |
                                  left       cur   right
```

1.  `A[cur] = 1`：已经就位，`cur++`即可; 

2.  `A[cur] = 0`：交换`A[cur]`和`A[left]`。由于`A[left]=1`或`left=cur`，所以交换以后`A[cur]`已经就位，`cur++`，`left++`; 

3.  `A[cur] = 2`：交换`A[cur]`和`A[right]`，`right--`。由于`xm`的值未知，`cur`不能增加，继续判断`xm`，`cur > right`扫描结束；

<br>

#### Java Code:
```java
    class Solution {
        public void sortColors(int[] nums) {
            int i0 = 0, i2 = nums.length - 1;
            int i = 0;
            while (i <= i2 && i0 < i2) {
                if (nums[i] == 0) swap(nums, i0++, i++);
                else if (nums[i] == 2) swap(nums, i2--, i);
                else i++;
            }
        }
        private void swap(int[] nums, int i, int j) {
            int t = nums[i];
            nums[i] = nums[j];
            nums[j] = t;
        }
    }
```
    