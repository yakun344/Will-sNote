## Minimum Depth of Binary Tree
_update Jan 20,2018  10:24_

---
[LeetCode](https://leetcode.com/problems/minimum-depth-of-binary-tree/description/)

Given a binary tree, find its minimum depth.

The minimum depth is the number of nodes along the shortest path from the root node down to the nearest leaf node.

<br>

### Basic Idea:
这道题目的**陷阱**是要判断一个node是child需要判断他左右子树都为null，此时才能得到他的height。

**两种思路： ** DFS 和 BFS。我认为 BFS 会比较好，因为BFS一定会先找到 minimum depth 之后就可以直接返回，而 DFS 需要搜索整个二叉树。

### Java Code:
```java
