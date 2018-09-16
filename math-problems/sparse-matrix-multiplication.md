# Sparse Matrix Multiplication
_update Sep 16 2018, 9:58_

---
[LeetCode](https://leetcode.com/problems/sparse-matrix-multiplication/description/)

Given two sparse matrices A and B, return the result of AB.

You may assume that A's column number is equal to B's row number.

**Example:**

    Input:

    A = [
      [ 1, 0, 0],
      [-1, 0, 3]
    ]

    B = [
      [ 7, 0, 0 ],
      [ 0, 0, 0 ],
      [ 0, 0, 1 ]
    ]

    Output:

         |  1 0 0 |   | 7 0 0 |   |  7 0 0 |
    AB = | -1 0 3 | x | 0 0 0 | = | -7 0 3 |
                      | 0 0 1 |

### Basic Idea:
如果按照正常求矩阵点积的方法一个元素一个元素计算的话，需要计算AB中所有对应位置元素的乘积，但是由于给定矩阵是sparse的，会有很多 0，我们注意到对于 A 中的一个 0，我们就无需计算和该 0 有关的乘积，也就是对应B中的一个row都无需和该A中的0相乘。利用这条性质可以简化时间。

#### Java Code:
```java
class Solution {
    public int[][] multiply(int[][] A, int[][] B) {
        int R = A.length, C = B[0].length;
        int[][] res = new int[R][C];
        for (int r = 0; r < A.length; ++r) {
            for (int c = 0; c < A[0].length; ++c) {
                // 如果A[r][c]!=0，则计算A[r][c]和B中第c行每个数字的和，结果累加到res的第r行相应元素
                // 如果为0，则B中第c行每个元素都不需要再乘，节省操作
                if (A[r][c] != 0) {
                    for (int i = 0; i < C; ++i) {
                        res[r][i] += A[r][c] * B[c][i];
                    }
                }
            }
        }
        return res;
    }
}
```
