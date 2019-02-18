# Cousins in Binary Tree
_update Feb 18 2019, 1:40_

---
[LeetCode](https://leetcode.com/problems/cousins-in-binary-tree/)

In a binary tree, the root node is at depth 0, and children of each depth k node are at depth k+1.

Two nodes of a binary tree are cousins if they have the same depth, but have different parents.

We are given the root of a binary tree with unique values, and the values x and y of two different nodes in the tree.

Return true if and only if the nodes corresponding to the values x and y are cousins.

**Example 1:**

![](https://assets.leetcode.com/uploads/2019/02/12/q1248-01.png)

    Input: root = [1,2,3,4], x = 4, y = 3
    Output: false

**Example 2:**

![](https://assets.leetcode.com/uploads/2019/02/12/q1248-02.png)

    Input: root = [1,2,3,null,4,null,5], x = 5, y = 4
    Output: true

**Example 3:**

![](https://assets.leetcode.com/uploads/2019/02/13/q1248-03.png)

    Input: root = [1,2,3,null,4], x = 2, y = 3
    Output: false
 

**Note:**

1. The number of nodes in the tree will be between 2 and 100.
2. Each node has a unique integer value from 1 to 100.

<br/>

## Basic Idea:
用一个helper function，返回所要找的node的depth和parent。分别用helper找到两个node的信息，然后判断它们是否有相同的深度不同的parent。

#### Java Code:
```java
    class Solution {
        public boolean isCousins(TreeNode root, int x, int y) {
            if (root == null) return false;
            int[] info_x = helper(root, null, 0, x);
            int[] info_y = helper(root, null, 0, y);
            if (info_x == null || info_y == null) return false;
            return info_x[0] == info_y[0] && info_x[1] != info_y[1];
        }
        
        // ret[0]: depth
        // ret[1]: parent
        private int[] helper(TreeNode root, TreeNode parent, int depth, int target) {
            if (root == null) return null;
            if (root.val == target) return new int[]{depth, parent == null ? Integer.MAX_VALUE : parent.val};
            int[] left = helper(root.left, root, depth + 1, target);
            if (left != null) return left;
            int[] right = helper(root.right, root, depth + 1, target);
            return right;
        }
    }
```
