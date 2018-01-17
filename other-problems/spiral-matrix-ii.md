## Spiral Matrix II
_update Jan 17,2018 0:21_

---
[LeetCode](https://leetcode.com/problems/spiral-matrix-ii/description/)

Given an integer n, generate a square matrix filled with elements from 1 to n2 in spiral order.

**For example,**  

    Given n = 3,
    
    You should return the following matrix:
    [
     [ 1, 2, 3 ],
     [ 8, 9, 4 ],
     [ 7, 6, 5 ]
    ]
    
<br>

### Basic Idea:
这道题使用recursion的思路，一圈一圈向内填，主要传入两个参数，`offset` 和 `counter`, 其中 offset 用来表示向内偏移的圈数，而 counter 表示下一个要填入的数字。另外我们还需要传入 `width` 来约束 base case；

### Java Code:
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














