## N-Queens
_update Jan 16,2018  21:32_

---
[LeetCode](https://leetcode.com/problems/n-queens/description/)

The n-queens puzzle is the problem of placing n queens on an n×n chessboard such that no two queens attack each other.

![](/assets/8-queens.png)

Given an integer n, return all distinct solutions to the n-queens puzzle.

Each solution contains a distinct board configuration of the n-queens' placement, where `'Q'` and `'.'` both indicate a queen and an empty space respectively.

**For example,**

     There exist two distinct solutions to the 4-queens puzzle:
 
     [
      [".Q..",  // Solution 1
       "...Q",
       "Q...",
       "..Q."],
     
      ["..Q.",  // Solution 2
       "Q...",
       "...Q",
       ".Q.."]
     ]
   
<br>

### Basic Idea:
&emsp; 首先想到这是一道 dfs 的题目，因为要生成所有的解的集合。大致思路如下：  
&emsp; 首先明确queen的性质，她可以杀与其同在一条直线上的其他棋子（横竖斜），而我们要在 `N*N` 大小的棋盘中放入 N 个 queen，所以我们一定是每个 row 放一个 queen。然后，具体地，我们对每一个 row，考虑其上每个 col 位置放 queen 的话会不会影响之前放置好的第 `0 to row-1` row 上的queen。如果当前 `(row, col)` 可以，就继续向 `row + 1` 继续 dfs。

**时间空间复杂度分析：**  
&emsp; 整个算法如果是 brute force 解，求出所有可能的组合再判断是否 valid，需要 `O(N^N)` 的时间复杂度，因为在每一个 row，都需要考虑 N 个 col 的情况。但是因为我们可以每次判断当前 col 能否放置 queen，相当于在搜索的过程中进行了剪枝，所以我们至少可以将时间复杂度优化为 `O(N!)` (即不考虑之前放置过queen的col，所以每次可选col数量递减1)； 因为我们还剪掉了斜线上的情况，所以最后的时间复杂度应该是优于 `O(N!)`；  
&emsp; 空间的话，recursion 的 call stack 最大深度为 N，如果不考虑储存和处理解集的空间，整个空间复杂度应该是 `O(N)`;

#### Java Code:
```java




















