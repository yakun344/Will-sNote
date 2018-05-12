# Subtree of Another Tree
_update May 11,2018  21:58_

---
[LeetCode](https://leetcode.com/problems/subtree-of-another-tree/description/)


Given two non-empty binary trees s and t, check whether tree t has exactly the same structure and node values with a subtree of s. A subtree of s is a tree consists of a node in s and all of this node's descendants. The tree s could also be considered as a subtree of itself.

**Example 1:**

      Given tree s:
      
           3
          / \
         4   5
        / \
       1   2
      Given tree t:
         4 
        / \
       1   2
      Return true, because t has the same structure and node values with a subtree of s.

**Example 2:**
      
      Given tree s:
      
           3
          / \
         4   5
        / \
       1   2
          /
         0
      Given tree t:
         4
        / \
       1   2
      Return false.

<br>

### Basic Idea:
写一个 isSame 的 helper function，判断两个node所代表的是否是同样的树，然后递归调用。两层递归嵌套。

* #### C++ Code：

```cpp
class Solution {
    bool isSame(TreeNode* node1, TreeNode* node2) {
        if (node1 == nullptr && node2 == nullptr) return true;
        else if (node1 == nullptr || node2 == nullptr) return false;
        else if (node1->val != node2->val) return false;
        bool left = isSame(node1->left, node2->left);
        bool right = isSame(node1->right, node2->right);
        return left && right;
    }
public:
    bool isSubtree(TreeNode* s, TreeNode* t) {
        if (t == nullptr) return true;
        else if (s == nullptr) return false;
        if (s->val == t->val && isSame(s, t)) return true;
        else return isSubtree(s->left, t) || isSubtree(s->right, t);
    }
};
```