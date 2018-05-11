# Convert BST to Greater Tree
_update May 11,2018  15:29_

---
[LeetCode](https://leetcode.com/problems/convert-bst-to-greater-tree/description/)


Given a Binary Search Tree (BST), convert it to a Greater Tree such that every key of the original BST is changed to the original key plus sum of all keys greater than the original key in BST.

**Example:**
    
    Input: The root of a Binary Search Tree like this:
              5
            /   \
           2     13

    Output: The root of a Greater Tree like this:
             18
            /   \
          20     13
          
<br>

### Basic Idea:
将二叉树的每个节点的值换成大于等于它所有节点值的和，那么我们就需要在遍历到该节点之前获得比它大的所有节点值之和，注意到二叉搜索树的性质，我们只要进行逆向的 inorder traversal，就可以从大到小按照顺序遍历BST。维持一个全局变量sum跟踪当前所有较大 node value 的和，每次将新遍历到的node的value 加上 sum 就好。

* #### C++ Code:
```cpp
class Solution {
    int sum = 0;
public:
    // reverse in-order traversal
    TreeNode* convertBST(TreeNode* root) {
        if (root == nullptr) return root;
        convertBST(root->right);
        root->val = root->val + sum;
        sum = root->val; // 现在root->val就是包括之前root->val在内的大于它的node的val和了
        convertBST(root->left);
        return root;
    }
};
```