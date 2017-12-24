## Binary Tree Longest Consecutive Sequence 
_update Aug 27, 2017  21:12_

---
[LintCode](http://www.lintcode.com/en/problem/binary-tree-longest-consecutive-sequence/)   
[LeetCode](https://leetcode.com/problems/binary-tree-longest-consecutive-sequence/description/)

Given a binary tree, find the length of the longest consecutive sequence path.

The path refers to any sequence of nodes from some starting node to any node in the tree along the parent-child connections. The longest consecutive path need to be from parent to child (cannot be the reverse).

**Example**

    For example,
        
           1
            \
             3
            / \
           2   4
                \
                 5
    Longest consecutive sequence path is 3-4-5, so return 3.
         
            2
             \
              3
             / 
            2    
           / 
          1
    Longest consecutive sequence path is 2-3,not3-2-1, so return 2.
    
#### Basic Idea:
**思路1：普通dfs，top-down**
从上向下遍历每一条路径，传给下层当前连续序列长度和parent val，在下层判断，如果 root.val == parentVal + 1, 则 length+=1；

Java Code:
```java    
    class Solution {
        private int maxLength;
        public int longestConsecutive(TreeNode root) {
            maxLength = 0;
            helper(root, 0, Integer.MIN_VALUE);
            return maxLength;
        }
        private void helper(TreeNode root, int currLength, int parentVal) {
            if (root == null) return;
            if (root.val == parentVal + 1) {
                currLength++;
            } else {
                currLength = 1;
            }
            maxLength = Math.max(maxLength, currLength);
            helper(root.left, currLength, root.val);
            helper(root.right, currLength, root.val);
        }
    }
```

**思路2：分治法**
先看左右两子树的当前连续序列长度，如果可以延长，则延长。和上一种思路其实类似；

Java
```java
    class Solution {
        private int currMaxLength;
        public int longestConsecutive(TreeNode root) {
            if (root == null) return 0;
            this.currMaxLength = Integer.MIN_VALUE;
            helper(root);
            return this.currMaxLength;
        }
        
        // return the length of consecutive sequence start from this node
        // update the global variable along the way
        private int helper(TreeNode root) {
            if (root == null) return 0;
            int leftLength = helper(root.left);
            int rightLength = helper(root.right);
            int ret = 1;
            if (root.left != null && root.val == root.left.val - 1) ret = leftLength + 1;
            if (root.right != null && root.val == root.right.val - 1) ret = Math.max(ret, rightLength + 1);
            if (ret > this.currMaxLength) {
                this.currMaxLength = ret;
            }
            return ret;
        }
    }
```
    