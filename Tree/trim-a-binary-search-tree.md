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
-  当root==null 时，返回null；
-  当root.val>R 时，抛弃root及其右子树，返回 trim(root.left)
-  当root.val<L 时，抛弃root及其左子树，返回 trim(root.right)
-  然后递归 trim root 的 左右子树；

#### Java Code:
```java
    class Solution {
        public TreeNode trimBST(TreeNode root, int L, int R) {
            if (root == null) return null;
            if (root.val < L) return trimBST(root.right, L, R);
            else if (root.val > R) return trimBST(root.left, L, R);
            else {
                root.left = trimBST(root.left, L, R);
                root.right = trimBST(root.right, L, R);
            }
            return root;
        }
    }
```   
     
     
     
     
     
     
     
     
     
     
     
     
     
     
     