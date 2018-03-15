# Largest Sub-Matrix Sum
_update Mar 15, 2018 15:56_

---
Given a matrix that contains integers, find the submatrix with the largest sum.

Return the sum of the submatrix.

**Assumptions:**

The given matrix is not null and has size of M * N, where M >= 1 and N >= 1

**Examples**

    { {1, -2, -1, 4},
    
      {1, -1,  1, 1},
    
      {0, -1, -1, 1},
    
      {0,  0,  1, 1} }
    
    the largest submatrix sum is (-1) + 4 + 1 + 1 + (-1) + 1 + 1 + 1 = 7.
    
<br>

## Basic Idea:
这道题目就是要求一个矩阵中所有子矩阵中元素的最大和。

* #### Naive Solution, brute force `O(n^6)`:
如果不用任何技巧，求解这道题我们需要 `O(N^6)` (N 为边长)。因为需要四重 for 循环考虑 `O(N^4)` 个所有的submatrix，对于每个 苏北matrix，又需要 `O(N^2)` 的时间求和，一共 `O(N^6)`;

* #### 使用一维 prefix sum 数组优化 `O(n^5)`：
如果我们为每行建一个 prefix sum 数组，我们就可以用 `O(N)` 的时间求得任意一个 submatrix 的所有元素之和，如此一来时间可以优化为 `O(N^5)`；

* #### 使用二维 prefix sum 数组优化 `O(n^4)`：
试想如果有一个 `2D prefix sum matrix : sums = int[][]`，满足 `sums[i][j] = sum{matrix 从[0,0] 到 [i,j] 的子矩阵}`，则我们可以用 `O(1)` 的时间求得任意 submatrix 的和。而我们可以用 `O(N^2)` 的时间来求得sums 数组，如此一来，就将时间复杂度优化为 `O(N^4)` 如下图所示：

![](/assets/WechatIMG2.jpg)

（Code 见后面）

* #### 依然用一维 prefix sum 数组优化，结合 Kadane 求 max subarray sum 的算法 `O(n^3)`:
与之前的几种方法不同，这次不再遍历每一个 submatrix，而是枚举所有的左右边界对，在每一对左右边界之间，求不同上下边界组成的 submatrix 的最大和，全局最大就是解。
<br>
具体地，我们可以先用 `O(N^2)` 的时间求所有 row 的 prefix sum 数组，如此一来，对于任意给定左右边界的某一行，我们可以用 `O(1)` 时间求和。那么，我们可以把任意左右边界之间的部分视为一个竖着的一维数组，其中的值就是某一行的和。于是，我们就可以使用 Kadane 算法求它的 max subarray sum，该过程耗时 `O(N)`，而因为共有 `O(N^2)` 个左右边界组合，一共要进行该过程 `O(N^2)` 次，所以总时间复杂度为 `O(N^3)`。

