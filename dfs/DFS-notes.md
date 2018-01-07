## DFS notes
_update Jan 7, 2017  16:05_

---
### Basic Idea
DFS 是一种搜索算法，一个非常好的应用是用来解决排列组合问题。

写 DFS 的时候要注意两个要点：

1.  What does it store on each level, 每层的意义以及总的递归深度；
2.  How many different states should we try to put on each level，即每层有多少个不同的分支需要继续；

<br>

### 经典题目：
#### 1. [Subsets of a set](https://will-gxz.gitbooks.io/xiaozheng_algo/content/dfs/permutation-and-combination/subsets.html)

按照之前的两点考虑，例如输入是一个包含三个元素的set：{'a', 'b', 'c'}， 则：

  *  一共有三层 dfs，每层考虑该位置的元素是否包含；
  *  每层有两个分支，即包含或者不包含；











