# Second Minimum Node In a Binary Tree \(Easy LinkedIn\)

_update May 13,2018 1:18_

[LeetCode](https://leetcode.com/problems/second-minimum-node-in-a-binary-tree/description/)

Given a non-empty special binary tree consisting of nodes with the non-negative value, where each node in this tree has exactly two or zero sub-node. If the node has two sub-nodes, then this node's value is the smaller value among its two sub-nodes.

Given such a binary tree, you need to output the second minimum value in the set made of all the nodes' value in the whole tree.

If no such second minimum value exists, output -1 instead.

**Example 1:**

```text
Input:
    2
   / \
  2   5
     / \
    5   7

Output: 5
Explanation: The smallest value is 2, the second smallest value is 5.
```

**Example 2:**

```text
Input:
    2
   / \
  2   2

Output: -1
Explanation: The smallest value is 2, but there isn't any second smallest value.
```

## Basic Idea:

这道题的设计比较特殊，定义了一种很特别的树，每个node的值等于其左右子节点值中较小的一个，要求整个树中第二小的值。

用递归的思路求解。首先，对于 root 节点来说，第二小的节点一定来自于其左右两子树，所以基本思路是让左右子树各提供一个候选值，返回较小的。

* **C++ Code:**

  ```cpp
  class Solution {
  public:
    int findSecondMinimumValue(TreeNode* root) {
        if (root->left == nullptr && root->right == nullptr) {
            return -1;
        }
        int left = root->left->val == root->val ? findSecondMinimumValue(root->left) : root->left->val;
        int right = root->right->val == root->val ? findSecondMinimumValue(root->right) : root->right->val;
        // 只有当左或者右val等于parent的时候，才可能会出现left或right为-1的情况
        if (left == -1 && right == -1) return -1;
        else if (left == -1) return right;
        else if (right == -1) return left;
        else return min(left, right);
    }
  };
  ```

