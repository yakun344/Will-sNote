# Third Maximum Number

---
_update 2018-10-08 22:18:27_

[LeetCode](https://leetcode.com/problems/third-maximum-number/description/)

Given a non-empty array of integers, return the third maximum number in this array. If it does not exist, return the maximum number. The time complexity must be in O(n).

**Example 1:**

    Input: [3, 2, 1]

    Output: 1

    Explanation: The third maximum is 1.

**Example 2:**

    Input: [1, 2]

    Output: 2

    Explanation: The third maximum does not exist, so the maximum (2) is returned instead.

**Example 3:**

    Input: [2, 2, 3, 1]

    Output: 1

    Explanation: Note that the third maximum here means the third maximum distinct number.
        Both numbers with value 2 are both considered as second maximum.

<br/>

#### Basic Idea:
维持三个变量，扫描数组的同时手动维持前三大数字的关系。注意题目所求的是三个不同的数字，所以如果遇到和已有的三个变量数字相等的情况直接跳过就好。

* #### Java Code:
  ```java
  class Solution {
      public int thirdMax(int[] nums) {
          long first = 2 * (long)Integer.MIN_VALUE, second = 2 * (long)Integer.MIN_VALUE, third = 2 * (long)Integer.MIN_VALUE;
          for (int num : nums) {
              if (num == first || num == second || num == third) continue;
              if (num > first) {
                  third = second;
                  second = first;
                  first = num;
              } else if (num > second) {
                  third = second;
                  second = num;
              } else if (num > third) {
                  third = num;
              }
          }
          return third > 2 * (long)Integer.MIN_VALUE ? (int)third : (int)first;         
      }
  }
  ```
