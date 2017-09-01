## Delete Node in a BST
_update Sep 1, 2017  17:11_

---
[LeetCode](https://leetcode.com/problems/delete-node-in-a-bst/description/)

Given a root node reference of a BST and a key, delete the node with the given key in the BST. Return the root node reference (possibly updated) of the BST.

Basically, the deletion can be divided into two stages:

Search for a node to remove.
If the node is found, delete the node.
**Note:** Time complexity should be O(height of tree).

**Example:**

        root = [5,3,6,2,4,null,7]
        key = 3
        
            5
           / \
          3   6
         / \   \
        2   4   7
        
        Given key to delete is 3. So we find the node with value 3 and delete it.
        
        One valid answer is [5,4,6,2,null,null,7], shown in the following BST.
        
            5
           / \
          4   6
         /     \
        2       7
        
        Another valid answer is [5,2,6,null,4,null,7].
        
            5
           / \
          2   6
           \   \
            4   7
            
#### Basic Idea:
[这里](http://www.algolist.net/Data_structures/Binary_search_tree/Removal) 有一个关于在BST中删除节点算法的介绍；

我们知道，在BST中删除节点分为三种情况：
-  target node 没有子节点：  此时可以直接删除
-  target node 有一个子节点： 直接用子节点替换target节点
-  target node 有两个子节点：这种情况最为复杂。方法是用其右子树中最小元素（successor）复制后代替target，然后递归删除之前复制过的successor；





