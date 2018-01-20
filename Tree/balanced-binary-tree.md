## Balanced Binary Tree
_update Aug 27,2017  15:26_

---
[LintCode](http://www.lintcode.com/en/problem/balanced-binary-tree/)     
[LeetCode](https://leetcode.com/problems/balanced-binary-tree/description/)

Given a binary tree, determine if it is height-balanced.

For this problem, a height-balanced binary tree is defined as a binary tree in which the depth of the two subtrees of every node never differ by more than 1.

**Example**

         Given binary tree A = {3,9,20,#,#,15,7}, B = {3,#,20,15,7}
         
         A)  3            B)    3 
            / \                  \
           9  20                 20
             /  \                / \
            15   7              15  7
            
         The binary tree A is a height-balanced binary tree, but B is not.
         
#### Basic Idea:
采用分治法，分别考虑左右两子树的返回值。如果两子树返回值有 -1，或者两者返回值只差大于 1，返回 -1（-1 表示已经不是平衡树）；否则返回 max(left, right) + 1；

#### Python Code：
```python
    class Solution:
        """
        @param: root: The root of binary tree.
        @return: True if this Binary tree is Balanced, or false.
        """
        def isBalanced(self, root):
            def helper(root):
                if not root:
                    return 0
                left = helper(root.left)
                right = helper(root.right)
                if left == -1 or right == -1 or abs(left - right) > 1:
                    return -1
                return max(left, right) + 1
                
            return helper(root) != -1
```
            