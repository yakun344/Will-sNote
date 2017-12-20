## Lowest Common Ancestor III
_update Jul 14, 2017 16:00_

---
[LintCode](http://www.lintcode.com/en/problem/lowest-common-ancestor-iii/)  
[LeetCode](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/description/): leetcode 中的版本没有考虑 two node 不存在树中的情况。

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

---
_update Aug 27, 2017  16:08_

更新python的解，对上面的java解法进一步简化，完全的按照可能出现的情况分情况讨论，思路更加自然；
#### Python
```python
    class Solution:
        """
        @param {TreeNode} root The root of the binary tree.
        @param {TreeNode} A and {TreeNode} B two nodes
        @return Return the LCA of the two nodes.
        """ 
        def lowestCommonAncestor3(self, root, A, B):
            def helper(root):
                if not root:
                    return None
                left = helper(root.left)
                right = helper(root.right)
                # 如果 A==B，自己是ancestor
                if root == A and root == B:
                    flag[0] = flag[1] = True
                    return root
                if root == A:
                    flag[0] = True
                    return root
                if root == B:
                    flag[1] = True
                    return root
                # 处理过各种情况之后再考虑返回
                if (left == A and right == B) or (left == B and right == A):
                    return root
                # 如果当前root不是lowest ancestor，则返回l和r中非null的，若都是null，则返回null
                return left if left else right
                
            flag = [False] * 2
            ret = helper(root)
            if not flag[0] or not flag[1]:
                return None
            return ret
```
<br>

---
_update Dec 20,2017  1:31_

### Update
这种写法很暴力地用判断语句枚举了各种情况，也比较符合 intuition;
**Java Code:**
```java
    public class Solution {
        /*
         * @param root: The root of the binary tree.
         * @param A: A TreeNode
         * @param B: A TreeNode
         * @return: Return the LCA of the two nodes.
         */
        private TreeNode ret;
        public TreeNode lowestCommonAncestor3(TreeNode root, TreeNode p, TreeNode q) {
            helper(root, p, q);
            return ret;
        }
        
        private TreeNode helper(TreeNode root, TreeNode p, TreeNode q) {
            if (root == null) return null;
            TreeNode left = helper(root.left, p, q);
            TreeNode right = helper(root.right, p, q);
            // 如果 p==q，自己是 ancestor
            if (p == q && p == root) {
                ret = root;
                return null;
            // 如果 root, left, right 中有 p 和 q，则说明root就是LCA
            } else if (root == p && (left == q || right == q) ||
                root == q && (left == p || right == p) ||
                left == p && right == q ||
                left == q && right == p) {
                ret = root;
                return null;
            } else if (root == p || left == p || right == p){
                return p;
            } else if (root == q || left == q || right == q) {
                return q;
            } else {
                return null;
            }
        }
    }
```