## 498. Diagonal Traverse
_update Jul,29 2017 20:40_

---
[leetCode](https://leetcode.com/problems/diagonal-traverse/description/)

Given a matrix of M x N elements (M rows, N columns), return all elements of the matrix in diagonal order as shown in the below image.

**Example:**

    Input:
    [
     [ 1, 2, 3 ],
     [ 4, 5, 6 ],
     [ 7, 8, 9 ]
    ]
    Output:  [1,2,4,7,5,3,6,8,9]

**Explanation:**
   就是从【0，0】开始，沿右上方向遍历。

**Note:**
The total number of elements of the given matrix will not exceed 10,000.

#### Basic Idea:
我们注意到当 `（r + c）% 2 == 0` 的时候是向上的，否则就是向下。一次为分类，讨论不同的边界条件。

#### Python Code:
    ```python
    class Solution(object):
        def findDiagonalOrder(self, matrix):
            """
            :type matrix: List[List[int]]
            :rtype: List[int]
            """
            if not matrix or not matrix[0]:
                return []
            r, c = 0, 0
            M = len(matrix)
            N = len(matrix[0])
            res = []
            while True:
                res.append(matrix[r][c])
                if r == M - 1 and c == N - 1:
                    return res
                if (r + c) % 2 == 0: # go upper
                    if c == N - 1:
                        r += 1
                    elif r == 0:
                        c += 1
                    else:
                        r -= 1
                        c += 1
                else: # go down
                    if r == M - 1:
                        c += 1
                    elif c == 0:
                        r += 1
                    else:
                        r += 1
                        c -= 1
``` 
