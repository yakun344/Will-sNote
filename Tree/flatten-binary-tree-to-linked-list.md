## Flatten Binary Tree to Linked List
_update Jul 14, 2017 14:43_

---
[Lintcode](http://www.lintcode.com/en/problem/flatten-binary-tree-to-linked-list/)

Flatten a binary tree to a fake "linked list" in pre-order traversal.

Here we use the right pointer in TreeNode as the next pointer in ListNode.

** Notice**

Don't forget to mark the left child of each node to null. Or you will get Time Limit Exceeded or Memory Limit Exceeded.


**Example:**

                  1
                   \
         1          2
        / \          \
       2   5    =>    3
      / \   \          \
     3   4   6          4
                         \
                          5
                           \
                            6 
                            
#### Basic Idea:
使用分治法。先假设题目所给定的函数可以把一个二叉树变为如上图所示，我们可以先分别用该函数把root.left 和 root.right两棵子树变为这种形式，再将其拼接成最终形式。同时，我们注意到当我们完成左右两子树的flatten之后，如果左子树不存在，我们已经可以直接返回，所以要加上这一判断条件。

#### Java Code:
```java
    public class Solution {
        /**
         * @param root: a TreeNode, the root of the binary tree
         * @return: nothing
         */
         
    // 使用分治法解决问题 
        public void flatten(TreeNode root) {
            if (root == null) return;
            flatten(root.left); // flatten 左子树
            flatten(root.right); // flatten 右子树
            if (root.left == null) return;
            TreeNode leftLowest = root.left;
            while (leftLowest.right != null) {
                leftLowest = leftLowest.right;
            }
            leftLowest.right = root.right;
            root.right = root.left;
            root.left = null;
        }
    }
```