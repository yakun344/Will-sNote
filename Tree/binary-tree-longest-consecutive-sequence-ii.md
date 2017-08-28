## Binary Tree Longest Consecutive Sequence II
_update Aug 28, 2017  16:30_

---
[LintCode](http://www.lintcode.com/en/problem/binary-tree-longest-consecutive-sequence-ii/)
[LeetCode](https://leetcode.com/problems/binary-tree-longest-consecutive-sequence-ii/description/)

Given a binary tree, find the length of the longest consecutive sequence path.

The path could be start and end at any node in the tree

**Example**

                1
               / \
              2   0
             /
            3
         
         Return 4 // 0-1-2-3
         
#### Basic Idea:
考虑到这道题目除了普通的从上到下的path之外，还考虑到了可以拐弯的倒“v”型path，我们使用分治法比较容易理解；

写一个helper function，做到：
-  返回两个值，分别代表从当前node往下递增序列和递减序列的长度；
-  在函数内部，每次需要验证当前node和左右两子树能否组成一条符合条件的倒“v”型path，维持一个全局变量 maxLength，每次更新；

#### Java Code：
```java
    public class Solution {
        /**
         * @param root the root of binary tree
         * @return the length of the longest consecutive sequence path
         */
        private class RetType {
            int desc; // 从当前node开始，从上到下递减序列长度
            int asc; // 递增长度
            public RetType(int desc, int asc) {
                this.desc = desc;
                this.asc = asc;
            }
        }
        
        private int maxLength;
        
        public int longestConsecutive2(TreeNode root) {
            maxLength = 0;
            helper(root);
            return maxLength;
        }
        private RetType helper(TreeNode root) {
            if (root == null) return null;
            if (root.left == null && root.right == null) {
                return new RetType(1, 1);
            }
            RetType left = helper(root.left);
            RetType right = helper(root.right);
            int desc = 1;
            int asc = 1;
        
            // 更新当前node开始向下的递增和递减path的长度
            if (root.left != null && root.val == root.left.val + 1) {
                desc = left.desc + 1;
            }
            if (root.left != null && root.val == root.left.val - 1) {
                asc = left.asc + 1;
            }
            if (root.right != null && root.val == root.right.val + 1) {
                desc = Math.max(desc, right.desc + 1);
            } 
            if (root.right != null && root.val == root.right.val - 1) {
                asc = Math.max(asc, right.asc + 1);
            }
            
            // 判断是否有满足条件的倒“v”path，并更新全局变量maxLength；
            if (left != null && right != null) {
                if (root.val == root.left.val - 1 && root.val == root.right.val + 1) {
                    int len = left.asc + 1 + right.desc;
                    maxLength = Math.max(maxLength, len);
                } else if (root.val == root.left.val + 1 && root.val == root.right.val - 1) {
                    int len = left.desc + 1 + right.asc;
                    maxLength = Math.max(maxLength, len);
                }
            }
            maxLength = Math.max(maxLength, Math.max(asc, desc));
            return new RetType(desc, asc);
        }
    }
```
#### Python Code:
```python
    class Solution(object):
        def longestConsecutive(self, root):
            """
            :type root: TreeNode
            :rtype: int
            """
            def helper(root):
                if not root: return None
                if not root.left and not root.right:
                    self.maxLength = max(self.maxLength, 1)
                    return (1, 1)
                left = helper(root.left)
                right = helper(root.right)
                asc = 1
                desc = 1
                
                if root.left and root.val == root.left.val + 1:
                    desc = left[0] + 1
                if root.left and root.val == root.left.val - 1:
                    asc = left[1] + 1
                if root.right and root.val == root.right.val + 1:
                    desc = max(desc, right[0] + 1)
                if root.right and root.val == root.right.val - 1:
                    asc = max(asc, right[1] + 1)
                    
                if root.left and root.right:
                    if root.val == root.left.val + 1 and root.val == root.right.val - 1:
                        self.maxLength = max(self.maxLength, left[0] + 1 + right[1])
                    elif root.val == root.left.val - 1 and root.val == root.right.val + 1:
                        self.maxLength = max(self.maxLength, left[1] + 1 + right[0])
                
                self.maxLength = max(self.maxLength, asc, desc)
                return (desc, asc)
            
            self.maxLength = 0
            helper(root)
            return self.maxLength
```