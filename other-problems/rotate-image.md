# Rotate Image

---
_update 2018-10-09 19:43:44_

[LeetCode](https://leetcode.com/problems/rotate-image/description/)

You are given an `n x n` 2D matrix representing an image.

Rotate the image by 90 degrees (clockwise).

**Note:**

You have to rotate the image in-place, which means you have to modify the input 2D matrix directly. DO NOT allocate another 2D matrix and do the rotation.

**Example 1:**

    Given input matrix =
    [
      [1,2,3],
      [4,5,6],
      [7,8,9]
    ],

    rotate the input matrix in-place such that it becomes:
    [
      [7,4,1],
      [8,5,2],
      [9,6,3]
    ]

**Example 2:**

    Given input matrix =
    [
      [ 5, 1, 9,11],
      [ 2, 4, 8,10],
      [13, 3, 6, 7],
      [15,14,12,16]
    ],

    rotate the input matrix in-place such that it becomes:
    [
      [15,13, 2, 5],
      [14, 3, 4, 1],
      [12, 6, 8, 9],
      [16, 7,10,11]
    ]

<br/>

#### Basic Idea:
先旋转最外面一圈，然后向里面一圈，直到中心。利用一个offset变量从0开始到`N/2`递增。

* ##### Java Code：
  ```java
  class Solution {
      public void rotate(int[][] matrix) {
          int N = matrix.length;
          for (int offset = 0; offset <= N / 2; ++offset) {
              for (int i = offset; i < N - offset - 1; ++i) {
                  int t = matrix[offset][i];
                  matrix[offset][i] = matrix[N - 1 - i][offset];
                  matrix[N - 1 - i][offset] = matrix[N - 1 - offset][N - 1 - i];
                  matrix[N - 1 - offset][N - 1 - i] = matrix[i][N - 1 - offset];
                  matrix[i][N - 1 - offset] = t;
              }
          }
      }
  }
  ```
