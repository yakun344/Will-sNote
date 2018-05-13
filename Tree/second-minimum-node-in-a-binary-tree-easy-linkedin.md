# Second Minimum Node In a Binary Tree
_update May 13,2018 1:18_

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
    
<br>

### Basic Idea:
这道题的设计比较特殊，定义了一种很特别的树，每个node的值等于其左右子节点值中较小的一个，要求整个树中第二小的值。

用递归的思路求解。首先，对于 root 节点来说，第二小的节点一定来自于其左右两子树，所以基本思路是让左右子树各提供一个候选值，返回较小的。

* #### C++ Code:
```cpp
    class Solution {
        public int findSecondMinimumValue(TreeNode root) {
            if (root == null) return -1;
            else if (root.left == null && root.right == null) return -1;
            int left, right;
            
            // 确定左边候选值
            if (root.left.val != root.val) left = root.left.val;
            else left = findSecondMinimumValue(root.left);
            // 确定右边候选值
            if (root.right.val != root.val) right = root.right.val;
            else right = findSecondMinimumValue(root.right);
            
            if (left == -1 && right == -1) return -1;
            else if (left == -1 || right == -1) {
                return left == -1 ? right : left;
            } else {
                return Math.min(left, right);
            }
        }
    }
```