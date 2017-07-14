## Lowest Common Ancestor III
_update Jul 14, 2017 16:00_

---
[LintCode](http://www.lintcode.com/en/problem/lowest-common-ancestor-iii/)

Given the root and two nodes in a Binary Tree. Find the lowest common ancestor(LCA) of the two nodes.
The lowest common ancestor is the node with largest depth which is the ancestor of both nodes.
Return null if LCA does not exist.

** Notice**

node A or node B may not exist in tree.
  
**Example**

    For the following binary tree:
      
        4
       / \
      3   7
         / \
        5   6
        
    LCA(3, 5) = 4
      
    LCA(5, 6) = 7
      
    LCA(6, 7) = 7

#### Basic Idea:
两个given node，A, B。helper函数遇到其中之一的时候即返回它，如果左右node的返回值都不为空，即说明当前node为LCA，返回当前node。这样也可以解决两node其中之一是LCA的情况。但是考虑到有可能LCA不存在，我们设定两个boolean分别表示我们找到了A或B，如果最终两者不全为true，则返回null。

### Java Code:
```java
    public class Solution {
        /**
         * @param root The root of the binary tree.
         * @param A and B two nodes
         * @return: Return the LCA of the two nodes.
         */
        private boolean Aexist = false;
        private boolean Bexist = false;
        public TreeNode lowestCommonAncestor3(TreeNode root, TreeNode A, TreeNode B) {
            TreeNode ret = helper(root, A, B);
            if (Aexist && Bexist) return ret;
            else return null;
        }
        private TreeNode helper(TreeNode root, TreeNode A, TreeNode B) {
            if (root == null) return null;
            TreeNode left = helper(root.left, A, B);
            TreeNode right = helper(root.right, A, B);
            if (left != null && right != null) {
                return root;
            }
            TreeNode ret = null;
        // 这么写两个if是为了解决A, B 相等，自己是自己的LCA的情况。
            if (root == A) {
                Aexist = true;
                ret = A;
            }
            if (root == B) {
                Bexist = true;
                ret = B;
            }
            if (ret != null) return ret;
            else {
                return left != null ? left : right != null ? right : null;
            }
        }
    }
```