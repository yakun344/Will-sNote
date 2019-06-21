# Longest Cross of 1's

_update Mar 12, 2018 19:44_

Given a matrix that contains only 1s and 0s, find the largest cross which contains only 1s, with the same arm lengths and the four arms joining at the central point.

Return the arm length of the largest cross.

**Assumptions**

The given matrix is not null, has size of `N * M, N >= 0` and `M >= 0`.

**Examples**

```text
{ {0, 0, 0, 0},

  {1, 1, 1, 1},

  {0, 1, 1, 1},

  {1, 0, 1, 1} }

the largest cross of 1s has arm length 2.
```

## Basic Idea:

为了获得最大的十字形，我们可以预处理matrix，生成四个dp table，`dp[r][c]` 分别表示到点 `[r, c]` 为止，来自左右上下四个方向的连续 1 的长度。如此一来，为了获得最大十字，我们只需要检查每个点的左右上下四个方向连续 1 的长度，取四个长度的最小值作为当前位置十字的arm length;

时间复杂度: `O(5n^2)`;

* **Java Code:**

  ```java
  public class Solution {
    public int largest(int[][] matrix) {
      int R = matrix.length, C = matrix[0].length;
      int[][] leftRight = new int[R][C];
      int[][] rightLeft = new int[R][C];
      int[][] upDown = new int[R][C];
      int[][] downUp = new int[R][C];

      // get length consecutive 1's from left to right for each row
      for (int r = 0; r < R; ++r) {
        for (int c = 0; c < C; ++c) {
          if (c > 0) {
            leftRight[r][c] = matrix[r][c] == 1 ? leftRight[r][c - 1] + 1 : 0;
          } else {
            leftRight[r][c] = matrix[r][c];
          }
        }
      }

      // get length consecutive 1's from right to left for each row
      for (int r = 0; r < R; ++r) {
        for (int c = C - 1; c >= 0; --c) {
          if (c < C - 1) {
            rightLeft[r][c] = matrix[r][c] == 1 ? rightLeft[r][c + 1] + 1 : 0;
          } else {
            rightLeft[r][c] = matrix[r][c];
          }
        }
      }

      // get length consecutive 1's from top to bottom for each col
      for (int r = 0; r < R; ++r) {
        for (int c = 0; c < C; ++c) {
          if (r > 0) {
            upDown[r][c] = matrix[r][c] == 1 ? upDown[r - 1][c] + 1 : 0;
          } else {
            upDown[r][c] = matrix[r][c];
          }
        }
      }

      // get length consecutive 1's from bottom to top for each col
      for (int r = R - 1; r >= 0; --r) {
        for (int c = 0; c < C; ++c) {
          if (r < R - 1) {
            downUp[r][c] = matrix[r][c] == 1 ? downUp[r + 1][c] + 1 : 0;
          } else {
            downUp[r][c] = matrix[r][c];
          }
        }
      }

      // calculate the max length of arm
      int ret = 0;
      for (int r = 0; r < R; ++r) {
        for (int c = 0; c < C; ++c) {
          int currArmLength = Math.min(Math.min(leftRight[r][c], rightLeft[r][c]), Math.min(upDown[r][c], downUp[r][c]));
          ret = Math.max(ret, currArmLength);
        }
      }
      return ret;
    }
  }
  ```

