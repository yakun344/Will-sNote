# Recover Binary Search Tree
_update Sep 1, 2017  21:52_

---
[LeetCode](https://leetcode.com/problems/recover-binary-search-tree/description/)

Two elements of a binary search tree (BST) are swapped by mistake.

Recover the tree without changing its structure.

**Note:**
A solution using O(n) space is pretty straight forward. Could you devise a constant space solution?

## Basic Idea:
#### **思路1( worst one, `O(n) space, O(nlogn) time`)：**   
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

#### **思路2(`O(1) space, O(n) time`)：**   
还是那句话，面对一道 hard 难度的题目，给出如上那么 “straight forward” 的解自然交不了差。要做到follow up的要求，我们需要知道一种叫做 Morris-Traversal 算法，这种算法可以实现 O(n) time and O(1) Space 的 Binary Tree Traversing；

[这里](http://www.cnblogs.com/AnnieKim/archive/2013/06/15/morristraversal.html) 有个关于Morris-Traversal Alg 的介绍；

盗图一张：
![](/assets/Screen Shot 2017-09-01 at 10.01.08 PM.png)

精髓就是每到一个节点，通过把它左子树中predecessor的右指针指向自己来实现向上的跟踪；

**Python Code:**
在实现的过程中，注意对不同的功能部分进行模块化，比如这里，我另外写了一个 `visit(prev, node)` 函数，这样可以让主要逻辑部分更加简洁易懂。

```python
class Solution:
    def recoverTree(self, root):
        """
        :type root: TreeNode
        :rtype: void Do not return anything, modify root in-place instead.
        """
        # 找到左子树中最大的，也就是当前node的predecessor
        def getPred(node):
            temp = node.left
            while temp and temp.right:
                if temp.right is node: # 说明pred.right == curr, 返回null，并恢复pred.right
                    temp.right = None
                    return None
                temp = temp.right
            return temp
        
        
        # visit given node, find two swiched nodes
        def visit(prev, node):
            if prev and prev.val > node.val:
                print(str(prev.val) + " " + str(node.val))
                if self.first is None:
                    self.first = prev
                    self.second = node # 这里是为了解决tree中只有两个node的情况
                else:
                    self.second = node
                    
        
        # morris inorder traversal, find target nodes along the way
        self.first, self.second = None, None
        prev = None
        curr = root
        while curr:
            pred = getPred(curr)
            # 这里包括了如下情况：
            #    curr.left == null (此时pred为null)
            #    curr 的 pred.right == curr，说明左边已经都visited，getPred（）会恢复pred.right为null，并返回null
            # 我们要做的都是visit curr，然后让curr右移
            if not pred:
                visit(prev, curr)
                prev = curr
                curr = curr.right
            # 剩下的情况就是pred.right == None，此时令其指向curr，然后curr左移
            else:
                pred.right = curr
                curr = curr.left
                
        
        t = self.first.val
        self.first.val = self.second.val
        self.second.val = t
```

**Java Code:**
```java
class Solution {
    private TreeNode FIRST;
    private TreeNode SECOND;
    
    public void recoverTree(TreeNode root) {
        TreeNode curr = root;
        TreeNode prev = null;
        while (curr != null) {
            TreeNode pred = getPred(curr);
            if (pred == null) {
                visit(prev, curr);
                prev = curr;
                curr = curr.right;
            } else {
                pred.right = curr;
                curr = curr.left;
            }
        }
        
        int temp = FIRST.val;
        FIRST.val = SECOND.val;
        SECOND.val = temp;
    }
    
    private void visit(TreeNode prev, TreeNode node) {
        if (prev != null && prev.val > node.val) {
            if (FIRST == null) {
                FIRST = prev;
                SECOND = node;
            } else {
                SECOND = node;
            }
        }
    }
    
    private TreeNode getPred(TreeNode node) {
        TreeNode temp = node.left;
        while (temp != null && temp.right != null) {
            if (temp.right == node) {
                temp.right = null;
                return null;
            }
            temp = temp.right;
        }
        return temp;
    }
}
```


##### Note
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

##### 该方法时间复杂度分析:
因为 Morris Traversal 的过程中我们最多见到每个 node 3 次，所以总的时间复杂度仍然为 O(n);

#### 思路3 (fastest, O(n) time/space)
这种思路最简单，就是直接 inorder 整个树，用**方法2中的方法记录fist和second**，然后互换值。
```java
class Solution {
    private TreeNode first, second, prev;
    public void recoverTree(TreeNode root) {
        inorder(root);
        int t = first.val;
        first.val = second.val;
        second.val = t;
    }
    
    private void inorder(TreeNode curr) {
        if (curr == null) return;
        inorder(curr.left);
        if (prev != null && prev.val > curr.val) {
            if (first == null) {
                first = prev;
                second = curr;
            } else {
                second = curr;
            }
        }
        prev = curr;
        inorder(curr.right);
    }
}
```