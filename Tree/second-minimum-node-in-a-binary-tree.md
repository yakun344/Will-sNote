## Second Minimum Node In a Binary Tree
_update Sep 16,2017  14:46_

---
[LeetCode](https://leetcode.com/problems/second-minimum-node-in-a-binary-tree/description/)

Given a non-empty special binary tree consisting of nodes with the non-negative value, where each node in this tree has exactly two or zero sub-node. If the node has two sub-nodes, then this node's value is the smaller value among its two sub-nodes.

Given such a binary tree, you need to output the second minimum value in the set made of all the nodes' value in the whole tree.

If no such second minimum value exists, output -1 instead.

**Example 1:**

    Input: 
        2
       / \
      2   5
         / \
        5   7

    Output: 5
Explanation: The smallest value is 2, the second smallest value is 5.

**Example 2:**

    Input: 
        2
       / \
      2   2

    Output: -1
Explanation: The smallest value is 2, but there isn't any second smallest value.

#### Basic Idea:
基本思路是recursion加divide and conquer。

当当前 node.val 和 root值（最小值）相同的时候，继续向下找第一个大于 root值 的node。当当前node.val!=root.val 时，说明找到了，返回。在向下找的过程中，要返回左右子树找到的值中较小的。

#### Python Code：
```python
    class Solution(object):
        def findSecondMinimumValue(self, root):
            """
            :type root: TreeNode
            :rtype: int
            """
            def helper(root):
                # 如果root和 minVal 相等，则继续向下，不相等则返回
                if not root:
                    return -1
                if root.val != self.minVal:
                    return root.val
                else:
                    left = helper(root.left)
                    right = helper(root.right)
                    if left == -1 and right == -1:
                        return -1
                    if left != -1 and right != -1:
                        return min(left, right)
                    if left != -1:
                        return left
                    if right != -1:
                        return right
                    
            if not root:
                return -1
            self.minVal = root.val
            return helper(root)
```    






