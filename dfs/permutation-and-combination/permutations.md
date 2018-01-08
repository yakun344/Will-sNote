## Permutations
_update Jan 8,2017  12:24_

---
[LeetCode](https://leetcode.com/problems/permutations/description/)

Given a collection of distinct numbers, return all possible permutations.

**For example,**

    [1,2,3] have the following permutations:
    [
      [1,2,3],
      [1,3,2],
      [2,1,3],
      [2,3,1],
      [3,1,2],
      [3,2,1]
    ]
    
#### Basic Idea (Swap-Swap):
首先思路一定是使用dfs，根据之前 DFS-notes 中所述的思路，我们考虑该dfs共有几层，每层代表什么，有几个分支：

 *  该 dfs 可以分为 n 层；
 *  每层表示一个 position 上的元素；
 *  每层的分支个数为当前位置所有候选元素的个数，根据 level==position，可知分支个数为 `n - level`；

具体实现的时候，不再采用之前的维持一个 `boolean[] used`，每次循环 n 次查找可用元素的方法（会造成`O(n^n)`的时间复杂度），而是用Swap-Swap的方法，即在每层递归中，我们用把选中的元素换到当前position的方法，将 input array 直接当做path，这样到了下一层，就可以在当前position到末尾选一个元素换到position，然后返回后在 backtrack 回去。

**时间复杂度为：** `O(n!)`, 根据数学方法分析，答案的规模为 `n!`；根据递归树分析，共有 n 层，每层每点分支个数从 n 递减到 0，为 `n * (n-1) * (n-2) * ... * 1 = n!`

#### Java Code:

