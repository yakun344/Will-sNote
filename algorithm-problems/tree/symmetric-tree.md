# Symmetric Tree

_update Jan 1, 2018_

[LeetCode](https://leetcode.com/problems/symmetric-tree/description/)

Given a binary tree, check whether it is a mirror of itself \(_ie, symmetric around its center_\).

For example, this binary tree `[1,2,2,3,4,4,3]` is symmetric:

```text
     1
    / \
   2   2
  / \ / \
 3  4 4  3
```

But the following `[1,2,2,null,3,null,3]` is not:

```text
    1
   / \
  2   2
   \   \
   3    3
```

**Note:**  
Bonus points if you could solve it both recursively and iteratively.

## Basic Idea:

对于这道题目，直接考虑对称的情况，左边的左边等于右边的右边，左边的右边等于右边的左边。

**思路 1：Iterative**  
用两个栈分别存放左右两边要比较的node，比较之后按照下一次要比较的顺序将他们的child push 进 stack。

Python Code:

```python
class Solution:
    def isSymmetric(self, root):
        """
        :type root: TreeNode
        :rtype: bool
        """
        if not root or not root.left and not root.right:
            return True
        elif not root.left or not root.right:
            return False
        stack1, stack2 = [], []
        stack1.append(root.left)
        stack2.append(root.right)
        while stack1 and stack2:
            left = stack1.pop()
            right = stack2.pop()
            if left.left and not right.right: return False
            if left.right and not right.left: return False
            if left.val != right.val:
                return False
            if left.left:
                stack1.append(left.left)
            if right.right:
                stack2.append(right.right)
            if left.right:
                stack1.append(left.right)
            if right.left:
                stack2.append(right.left)

        if stack1 or stack2:
            return False
        return True
```

**思路 2：Recursive**  
递归函数接收左右两个node，比较左边的左和右边的右，左边的右和右边的左，然后递归比较。  
时间复杂度O\(n\)，因为我们只visit每个node一次。

Java Code:

```java
class Solution {
    public boolean isSymmetric(TreeNode root) {
        if (root == null) return true;
        return helper(root.left, root.right);
    }

    private boolean helper(TreeNode left, TreeNode right) {
        if (left == null && right == null) return true;
        else if (left == null || right == null) return false;
        if (left.val != right.val) return false;
        return helper(left.left, right.right) && helper(left.right, right.left);
    }
}
```

