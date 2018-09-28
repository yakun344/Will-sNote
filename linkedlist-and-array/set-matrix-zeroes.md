# Set Matrix Zeroes
_update Sep 28 2018, 0:04_

---
[LeetCode](https://leetcode.com/problems/set-matrix-zeroes/description/)

Given a `m x n` matrix, if an element is 0, set its entire row and column to 0. Do it in-place.

*8Example 1:**

    Input:
    [
      [1,1,1],
      [1,0,1],
      [1,1,1]
    ]
    Output:
    [
      [1,0,1],
      [0,0,0],
      [1,0,1]
    ]

**Example 2:**

    Input:
    [
      [0,1,2,0],
      [3,4,5,2],
      [1,3,1,5]
    ]
    Output:
    [
      [0,0,0,0],
      [0,4,5,0],
      [0,3,1,0]
    ]

**Follow up:**

1. A straight forward solution using O(mn) space is probably a bad idea.
2. A simple improvement uses O(m + n) space, but still not the best solution. Could you devise a constant space solution?

<br/>

### Basic Idea:
直接考虑follow up中的空间优化，我们可以利用最上面的row来记录哪些col需要清零，另外用一个boolean变量记录最上面的row本身是否需要清零。从上到下逐行扫描，完成一行之后，将0出现的col标记在第0 row，然后清空当前row。

#### Java Code：
```java
class Solution {
    public void setZeroes(int[][] matrix) {
        boolean firstRowZeroOut = false;
        boolean currRowZeroOut = false;
        for (int i = 0; i < matrix[0].length; ++i) {
            if (matrix[0][i] == 0) firstRowZeroOut = true;
        }
        for (int r = 1; r < matrix.length; ++r) {
            currRowZeroOut = false;
            for (int c = 0; c < matrix[0].length; ++c) {
                if (matrix[r][c] == 0) {
                    currRowZeroOut = true;
                    matrix[0][c] = 0;
                }
            }
            if (currRowZeroOut) zeroOutRow(matrix, r);
        }
        for (int i = 0; i < matrix[0].length; ++i) {
            if (matrix[0][i] == 0) zeroOutCol(matrix, i);
        }
        if (firstRowZeroOut) zeroOutRow(matrix, 0);
    }

    private void zeroOutRow(int[][] matrix, int r) {
        for (int c = 0; c < matrix[0].length; ++c) matrix[r][c] = 0;
    }

    private void zeroOutCol(int[][] matrix, int c) {
        for (int r = 0; r < matrix.length; ++r) matrix[r][c] = 0;
    }
}
```
