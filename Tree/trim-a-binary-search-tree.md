## Trim a Binary Search Tree
_update Sep 10, 2017  22:32_

---
[LeetCode](https://leetcode.com/problems/trim-a-binary-search-tree/description/)

Given a binary search tree and the lowest and highest boundaries as L and R, trim the tree so that all its elements lies in [L, R] (R >= L). You might need to change the root of the tree, so the result should return the new root of the trimmed binary search tree.

**Example 1:**

        Input:
            1
           / \
          0   2

          L = 1
          R = 2

        Output:
            1
              \
               2
**Example 2:**

         Input:
            3
           / \
          0   4
           \
            2
           /
          1

          L = 1
          R = 3

        Output:
              3
             /
           2   
          /
         1

#### Basic Idea:
应该是一道最近出现的新题，有些新颖，但是其实发现了根源之后难度并不大。

我们采用 recursive 的方法。首先定义递归函数，这个函数需要做如下事情：  

-  接收一个参数 TreeNode root，返回该树 trim 之后的 root；

于是，我们的思路就是:  

1.  当`root==null` 时，返回`null`；
2.  当`root.val > R` 时，抛弃`root`及其右子树，返回 `trim(root.left)`；
3.  当`root.val < L` 时，抛弃`root`及其左子树，返回 `trim(root.right)`；
4.  然后递归 trim root 的 左右子树；

#### Java Code:
```java
class Solution {
    public TreeNode trimBST(TreeNode root, int L, int R) {
        if (root == null) {
            return root;
        } else if (root.val < L) {
            return trimBST(root.right, L, R);
        } else if (root.val > R) {
            return trimBST(root.left, L, R);
        } else {
            root.left = trimBST(root.left, L, R);
            root.right = trimBST(root.right, L, R);
            return root;
        }
    }
}
```
#### Python Code:
```python
class Solution:
    def trimBST(self, root, L, R):
        """
        :type root: TreeNode
        :type L: int
        :type R: int
        :rtype: TreeNode
        """
        if not root: return None
        if root.val < L:
            return self.trimBST(root.right, L, R)
        if root.val > R:
            return self.trimBST(root.left, L, R)
        else:
            root.left = self.trimBST(root.left, L, R)
            root.right = self.trimBST(root.right, L, R)
            return root
```
<br>

---
_update Dec 29, 2017 1:00_

### Update
几个月后又见到这道题，起初竟没有想到递归的解法，看得出此题还是很有迷惑性的。这道题如果用iterative的方法会比较麻烦，因为要根据 L 和 R 写两套逻辑，整个代码会比较长，但是用递归就很简洁。     

---

_update 2018-05-26 23:56:19_

#### C++ Code:
```cpp
  /**
   * Definition for a binary tree node.
   * struct TreeNode {
   *     int val;
   *     TreeNode *left;
   *     TreeNode *right;
   *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
   * };
   */
  class Solution {
  public:
      TreeNode* trimBST(TreeNode* root, int L, int R) {
          if (root == nullptr) return nullptr;
          if (root->val < L) {
              return trimBST(root->right, L, R);
          } else if (root->val > R) {
              return trimBST(root->left, L, R);
          } else {
              root->left = trimBST(root->left, L, R);
              root->right = trimBST(root->right, L, R);
              return root;
          }
      }
  };
```
