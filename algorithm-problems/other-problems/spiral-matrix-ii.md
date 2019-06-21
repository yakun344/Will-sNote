# Spiral Matrix II

_update Jan 17,2018 0:21_

[LeetCode](https://leetcode.com/problems/spiral-matrix-ii/description/)

Given an integer n, generate a square matrix filled with elements from 1 to n2 in spiral order.

**For example,**

```text
Given n = 3,

You should return the following matrix:
[
 [ 1, 2, 3 ],
 [ 8, 9, 4 ],
 [ 7, 6, 5 ]
]
```

## Basic Idea:

* **Solution 1： recursion：**

  这道题使用recursion的思路，一圈一圈向内填，主要传入两个参数，`offset` 和 `counter`, 其中 offset 用来表示向内偏移的圈数，而 counter 表示下一个要填入的数字。另外我们还需要传入 `width` 来约束 base case；

  * **Java Code:**

    ```java
    class Solution {
        public int[][] generateMatrix(int n) {
            int[][] matrix = new int[n][n];
            helper(matrix, 0, n, 1);
            return matrix;
        }

        // 用来做递归，offset为向中间偏移的层数, width 是当前层的宽和高
        // count 为当前层第一个需要填的数字(左上角)
        private void helper(int[][] matrix, int offset, int width, int count) {
            if (width == 0) return;
            if (width == 1) {
                matrix[offset][offset] = count;
                return;
            }
            // 按照上右下左的顺序填入数，从 count 开始
            // 这里到width-1是一个技巧，例如上面的矩阵，填入的顺序为 1,2; 3,4; 5,6; 7,8
            for (int i = 0; i < width - 1; ++i) matrix[offset][i + offset] = count++;
            for (int i = 0; i < width - 1; ++i) matrix[i + offset][matrix.length - offset - 1] = count++;
            for (int i = 0; i < width - 1; ++i) matrix[matrix.length - offset - 1][matrix.length - offset - 1 - i] = count++;
            for (int i = 0; i < width - 1; ++i) matrix[matrix.length - offset - 1 - i][offset] = count++;

            helper(matrix, offset + 1, width - 2, count);
        }
    }
    ```

* **Solution 2：iteration**

  用一个while循环，每层将size递减2，表示每行每列都缩小了两排，退出循环后size依奇偶而定是 0 或者 1，如果是 1，则手动在中间再添加一个元素。

  * **Java Code：**

    ```java
    public class Solution {
      public int[][] generateMatrix(int n) {
        int[][] matrix = new int[n][n];
        int offset = 0, size = n, count = 1;
        while (size > 1) {
          // top row, left to right
          for (int i = offset; i < offset + size - 1; ++i) {
            matrix[offset][i] = count++;
          }
          // right column, up to bottom
          for (int i = offset; i < offset + size - 1; ++i) {
            matrix[i][offset + size - 1] = count++;
          }
          // bottom row, right to left
          for (int i = offset + size - 1; i > offset; --i) {
            matrix[offset + size - 1][i] = count++;
          }
          // left column, bottom up
          for (int i = offset + size - 1; i > offset; --i) {
            matrix[i][offset] = count++;
          }
          // increase offset by 1, decrease size by 2
          offset += 1;
          size -= 2;
        }
        // if the side length is odd, read the middle element manually
        if (size != 0) {
          matrix[n / 2][n / 2] = count++;
        }
        return matrix;
      }
    }
    ```

_update Jan 28,2018 17:51_

## Update: Follow Up, what if input is `<M, N>` represent for width and height?

如果width和height不同，用recursion比较方便。需要在每次开始填上右下左四个边之前进行判断，看当前width或height是否是 1，如果是，则特别处理，填好该（行、列 或者仅仅正中间一个元素）之后直接返回；另外要注意递归出口，只要有 width 或者 height 之一变成 0 就说明已经完成了，即可返回。

* **Java Code**

  ```java
      public class Solution {
        public int[][] spiralGenerate(int m, int n) {
          int[][] matrix = new int[m][n];
          if (m == 0 || n == 0) return matrix;
          helper(matrix, 1, 0, n, m);
          return matrix;
        }

        private void helper(int[][] matrix, int count, int offset, int width, int height) {
          // 每次先判断是否有width或者height == 1
          if (width == 0 || height == 0) return;
          else if (width == 1 && height == 1) {
            matrix[offset][offset] = count;
            return;
          } else if (width == 1) {
            for (int i = offset; i < offset + height; ++i) {
              matrix[i][offset] = count++;
            }
            return;
          } else if (height == 1) {
            for (int i = offset; i < offset + width; ++i) {
              matrix[offset][i] = count++;
            }
            return;
          }
          // up
          for (int i = offset; i < offset + width - 1; ++i) {
            matrix[offset][i] = count++;
          }
          // right
          for (int i = offset; i < offset + height - 1; ++i) {
            matrix[i][offset + width - 1] = count++;
          }
          // bottom
          for (int i = offset + width - 1; i > offset; --i) {
            matrix[offset + height - 1][i] = count++;
          }
          // left
          for (int i = offset + height - 1; i > offset; --i) {
            matrix[i][offset] = count++;
          }
          helper(matrix, count, offset + 1, width - 2, height - 2);
        }
      }
  ```

_update Sep 6 2018, 14:05_

### Update Java Code

```java
class Solution {
    public List<Integer> spiralOrder(int[][] matrix) {
        List<Integer> res = new ArrayList<>();
        if (matrix.length == 0 || matrix[0].length == 0) return res;

        int R = matrix.length, C = matrix[0].length;
        int width = C, height = R, offset = 0;
        while (true) {
            if (width == 0 || height == 0) return res;
            else if (width == 1 && height == 1) {
                res.add(matrix[offset][offset]);
                return res;
            } else if (width == 1) {
                for (int i = offset; i < R - offset; ++i) res.add(matrix[i][offset]);
                return res;
            } else if (height == 1) {
                for (int i = offset; i < C - offset; ++i) res.add(matrix[offset][i]);
                return res;
            } else if (width < 0 || height < 0) return res;
            for (int i = offset; i < C - 1 - offset; ++i) res.add(matrix[offset][i]);
            for (int i = offset; i < R - 1 - offset; ++i) res.add(matrix[i][C - 1 - offset]);
            for (int i = C - offset - 1; i > offset; --i) res.add(matrix[R - offset - 1][i]);
            for (int i = R - offset - 1; i > offset; --i) res.add(matrix[i][offset]);
            offset++;
            width -= 2;
            height -= 2;
        }
    }
}
```

