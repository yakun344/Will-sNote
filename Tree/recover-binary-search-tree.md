## Recover Binary Search Tree
_update Sep 1, 2017  21:52_

---
[LeetCode](https://leetcode.com/problems/recover-binary-search-tree/description/)

Two elements of a binary search tree (BST) are swapped by mistake.

Recover the tree without changing its structure.

**Note:**
A solution using O(n) space is pretty straight forward. Could you devise a constant space solution?

#### Basic Idea:
**思路1：**   
虽然是一个比较简单的思路，但一开始我竟没有想出来。就是直接 inorder traverse，把node和val分别存入两个list，对val从小到大排序，然后依次对node list中的node赋值即可。

Python Code：
```python
    class Solution(object):
        def recoverTree(self, root):
            """
            :type root: TreeNode
            :rtype: void Do not return anything, modify root in-place instead.
            """
            def inorder(root):
                if not root: return
                inorder(root.left)
                inorder_nodes.append(root)
                inorder_vals.append(root.val)
                inorder(root.right)
            
            inorder_nodes = []
            inorder_vals = []
            inorder(root)
            inorder_vals.sort()
            for i in range(len(inorder_nodes)):
                inorder_nodes[i].val = inorder_vals[i]
```

**思路2：**   
还是那句话，面对一道 hard 难度的题目，给出如上那么 “straight forward” 的解自然交不了差。要做到follow up的要求，我们需要知道一种叫做 Morris-Traversal 算法，这种算法可以实现 O(n) time and O(1) Space 的 Binary Tree Traversing；

[这里](http://www.cnblogs.com/AnnieKim/archive/2013/06/15/morristraversal.html) 有个关于Morris-Traversal Alg 的介绍；

盗图一张：
![](/assets/Screen Shot 2017-09-01 at 10.01.08 PM.png)

精髓就是每到一个节点，通过把它左子树中predecessor的右指针指向自己来实现向上的跟踪；

Java Code:
```java
    class Solution {
        public void recoverTree(TreeNode root) {
            TreeNode First = null;
            TreeNode Second = null;
            TreeNode curr = root;
            TreeNode prev = null;
            // mirrors traverse， inorder traverse
            while (curr != null) {
                TreeNode pred = null;
                if (curr.left != null) pred = getPred(curr);
                // 如果pred存在且无右孩子，则令其指向curr，curr=curr.left
                if (pred != null && pred.right == null) {
                    pred.right = curr;
                    curr = curr.left;
                }
                // 如果pred有右孩子(右孩子一定就是curr)，或者curr没有左孩子，则visit curr
                
                // else if (pred == null || pred.right != null) {
                else {
                    // 找出两个互换的点
                    if (prev != null && curr.val < prev.val) {
                        if (First == null) {
                            First = prev;
                            Second = curr;
                        } else {
                            Second = curr;
                        }
                    }
                    // 继续进行
                    prev = curr;
                    if (pred != null) pred.right = null;
                    curr = curr.right;
                }
            }
            // 交换First和second的val
            int t = First.val;
            First.val = Second.val;
            Second.val = t;
        }
        private TreeNode getPred(TreeNode node) {
            TreeNode curr = node.left;
            while (curr.right != null && curr.right != node) {
                curr = curr.right;
            }
            return curr;
        }
    }
```
关于在便利过程中找first和second的方法，灵感来自 [这里](http://fisherlei.blogspot.com/2012/12/leetcode-recover-binary-search-tree.html);  
关键步骤：  
>Inorder traverse, keep the previous tree node,
Find first misplaced node by
```java
if ( current.val < prev.val )
   Node first = prev;
```
>Find second by
```java
if ( current.val < prev.val )
   Node second = current;
```
>After traversal, swap the values of first and second node. Only need two pointers, prev and current node. O(1) space.


