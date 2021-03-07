---
description: 'update Mar 6, 2021'
---

# Find Peak Element II

Given an integer matrix A which has the following features :

```text
The numbers in adjacent positions are different.
The matrix has n rows and m columns, n and m will not less than 3.
For all i < n, A[i][0] < A[i][1] && A[i][m - 2] > A[i][m - 1].
For all j < m, A[0][j] < A[1][j] && A[n - 2][j] > A[n - 1][j]
```

We define a position \[i, j\] is a peak if:

```text
A[i][j] > A[i + 1][j] && 
A[i][j] > A[i - 1][j] && 
A[i][j] > A[i][j + 1] && 
A[i][j] > A[i][j - 1]
```

Find a peak element in this matrix. Return the index of the peak. Example

**Example 1:**

```text
Input: 
[[1, 2, 3, 6, 5], 
[16,41,23,22, 6], 
[15,17,24,21, 7], 
[14,18,19,20,10], 
[13,14,11,10, 9]] 
Output: [1,1] Explanation: [2,2] is also acceptable. 
The element at [1,1] is 41, greater than every element adjacent to it.
```

**Example 2:**

```text
Input: 
[[1, 5, 3], 
[4,10, 9], 
[2, 8, 7] ] 
Output: [1,1] Explanation: There is only one peek.
```

**Challenge**

Solve it in O\(n+m\) time.

If you come up with an algorithm that you thought it is O\(nlogm\) or O\(mlogn\), can you prove it is actually O\(n+m\) or propose a similar but O\(n+m\) algorithm? Notice

Guarantee that there is at least one peak, and if there are multiple peaks, return any one of them.

### Basic Idea

注意到这道题中所给矩阵的每一行每一列都保证有山峰，并且保证peak存在。那么理论上我们只需要从某个位置出发，一直向高的地方走，就一定可以找到山峰。

于是我们可以利用二分法，对列进行二分，每次找出该列最大的元素，然后检查这个元素的左右两个元素，如果遇到比该元素更大的，那么就向那边继续进行二分。

### Java Code

```java
public class Solution {
    public List<Integer> findPeakII(int[][] A) {
        int R = A.length, C = A[0].length;
        int left = 1, right = C - 2;
        // 二分列，也可以二分行
        while (left + 1 < right) {
            int midCol = left + (right - left) / 2;
            int maxRow = getMaxRow(A, midCol);
            if (A[maxRow][midCol] < A[maxRow][midCol - 1]) {
                right = midCol;
            } else if (A[maxRow][midCol] < A[maxRow][midCol + 1]) {
                left = midCol;
            } else {
                return Arrays.asList(maxRow, midCol);
            }
        }
        int maxRowLeft = getMaxRow(A, left);
        int maxRowRight = getMaxRow(A, right);
        if (A[maxRowLeft][left] > A[maxRowRight][right]) {
            return Arrays.asList(maxRowLeft, left);
        } else {
            return Arrays.asList(maxRowRight, right);
        }
    }
    
    // 找到输入列中最大元素所在的行的index
    private int getMaxRow(int[][] arr, int col) {
        int maxIndex = 0, maxValue = Integer.MIN_VALUE;
        for (int i = 0; i < arr.length; ++i) {
            if (arr[i][col] > maxValue) {
                maxValue = arr[i][col];
                maxIndex = i;
            }
        }
        return maxIndex;
    }
}
```

