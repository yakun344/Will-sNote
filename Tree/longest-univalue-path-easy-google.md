# Longest Univalue Path
_update May 14,2018  14:34_

---
[LeetCode](https://leetcode.com/problems/longest-univalue-path/description/)

Given a binary tree, find the length of the longest path where each node in the path has the same value. This path may or may not pass through the root.

**Note:** The length of path between two nodes is represented by the number of edges between them.

**Example 1:**

              Input:
              
                            5
                           / \
                          4   5
                         / \   \
                        1   1   5
              Output:
              
              2

**Example 2:**

              Input:
              
                            1
                           / \
                          4   5
                         / \   \
                        4   4   5
              Output:
              
              2
**Note:** The given binary tree has not more than 10000 nodes. The height of the tree is not more than 1000.

<br>

### Basic Idea:
利用一个helper function，返回包含当前node在内的从下往上的最长连续长度，然后在每层递归中更新全局变量res。

* #### C++ Code

```cpp
    class Solution {
        int res = 0;
        int helper(TreeNode* root) {
            if (root == nullptr) return 0;
            int ret = 0;
            int left = helper(root->left);
            int right = helper(root->right);
            if (left != 0 && right != 0 && root->left->val == root->val && root->right->val == root->val) {
                res = max(res, 1 + left + right);
                ret = max(left, right) + 1;
            } else if (left != 0 && root->left->val == root->val) {
                ret = left + 1;
            } else if (right != 0 && root->right->val == root->val) {
                ret = right + 1;
            } else {
                ret = 1;
            }
            res = max(res, ret);
            return ret;
        }
    public:
        int longestUnivaluePath(TreeNode* root) {
            if (root == nullptr) return 0;
            helper(root);
            return res - 1; // res 是node个数，而所求是边数，所以要减一
        }
    };
```