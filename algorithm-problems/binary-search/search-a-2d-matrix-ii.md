# Search a 2D Matrix II

_update Jul 11, 2017 8:36_

[lintcode](http://www.lintcode.com/en/problem/search-a-2d-matrix-ii/)

Write an efficient algorithm that searches for a value in an m x n matrix, return the occurrence of it.

This matrix has the following properties:

Integers in each row are sorted from left to right. Integers in each column are sorted from up to bottom. No duplicate integers in each row or column.

**Example** Consider the following matrix:

```text
[
  [1, 3, 5, 7],
  [2, 4, 7, 8],
  [3, 5, 9, 10]
]
Given target = 3, return 2.
```

## Basic Idea:

根据这个matrix的性质，我们可以观察得出，对于左下角的元素，越往上越小，越往右越大，那么如果这个元素大于target，则这一行不会有target，如果这个元素小于target，则这一列不会有target。所以我们只要用这种方法每次移动一行或者一列，就可以在 O\(m+n\) 的时间内解决战斗。

## Java Code：

```java
    public class Solution {
        /**
         * @param matrix: A list of lists of integers
         * @param: A number you want to search in the matrix
         * @return: An integer indicate the occurrence of target in the given matrix
         */
        public int searchMatrix(int[][] matrix, int target) {
            // search from lower left corner
            if (matrix == null || matrix.length == 0) return 0;
            int m = matrix.length, n = matrix[0].length;
            int res = 0;
            int r = m - 1, c = 0;
            while (r >= 0 && c < n) {
                if (matrix[r][c] == target) {
                    res++;
                    r--;
                    c++;
                } 
                else if (matrix[r][c] > target) r--;
                else c++;
            }
            return res;
        }
    }
```

