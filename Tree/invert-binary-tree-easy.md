## Invert Binary Tree
_update Jan 22, 2018  16:51_

---
[LeetCode](https://leetcode.com/problems/invert-binary-tree/description/)

Invert a binary tree.

          4
        /   \
       2     7
      / \   / \
     1   3 6   9
     to
          4
        /   \
       7     2
      / \   / \
     9   6 3   1

**Trivia:**  
This problem was inspired by this original tweet by Max Howell:
>Google: 90% of our engineers use the software you wrote (Homebrew), but you can’t invert a binary tree on a whiteboard so fuck off.

<br>

### Basic Idea:
只是一个很简单的recursion，但是要注意预先保存root.left的node。

### Java Code:
```java
class Solution {
    public TreeNode invertTree(TreeNode root) {
        if (root == null) return null;
        TreeNode left = root.left;
        root.left = invertTree(root.right);
        root.right = invertTree(left);
        return root;
    }
}
```
