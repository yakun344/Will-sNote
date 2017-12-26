## Delete Node in a BST
_update Sep 1, 2017  17:11_

---
[LeetCode](https://leetcode.com/problems/delete-node-in-a-bst/description/)

Given a root node reference of a BST and a key, delete the node with the given key in the BST. Return the root node reference (possibly updated) of the BST.

Basically, the deletion can be divided into two stages:

-  Search for a node to remove.
-  If the node is found, delete the node.

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

具体实现的时候，因为没有parent指针，需要另外写一个函数 remove(targetNode, parentNode)，返回替换掉target的新node，用来应付root被移除的情况；

#### Python Code （新写法，模块化）:
```python
class Solution:
    def deleteNode(self, root, key):
        """
        :type root: TreeNode
        :type key: int
        :rtype: TreeNode
        """
        def remove(parent, target):
            # if target has no child
            if not target.left and not target.right:
                if parent.left == target: parent.left = None
                else: parent.right = None
                return
            # if target has two child
            # 从右子树中找到最小的，复制到之前位置，递归删除该最小node
            elif target.left and target.right:
                minParent, minNode = findRightMin(target)
                newTarget = TreeNode(minNode.val)
                newTarget.left = target.left
                newTarget.right = target.right
                if parent.left == target: parent.left = newTarget
                else: parent.right = newTarget
                if minParent.val == target.val:
                    remove(newTarget, minNode)
                else:
                    remove(minParent, minNode)
            
            # if target has only left child
            elif target.left:
                if parent.left == target: parent.left = target.left
                else: parent.right = target.left
            
            # if target has only right child
            else:
                if parent.left == target: parent.left = target.right
                else: parent.right = target.right
            
        
        # return parent, minNode in right sub tree
        def findRightMin(root):
            parent, node = root, root
            node = node.right
            while node.left:
                parent = node
                node = node.left
            return parent, node
        
        
        # return target's parent, targetNode
        def findNode(key):
            parent, target = root, root
            while target and target.val != key:
                if target.val < key:
                    parent = target
                    target = target.right
                else:
                    parent = target
                    target = target.left
            return parent, target
        
        # 如果 target 是 root，另外建一个
        if not root: return None
        if root.val == key:
            dummy = TreeNode(0)
            dummy.right = root
            remove(dummy, root)
            root = dummy.right
        else:
            parent, target = findNode(key)
            if not target: return root
            remove(parent, target)
        return root
```

#### Java Code （最初的写法）:
```java
    class Solution {
        public TreeNode deleteNode(TreeNode root, int key) {
            // 先找到 key
            TreeNode target = root;
            TreeNode parent = root;
            while (target != null && target.val != key) {
                parent = target;
                if (key < target.val) {
                    target = target.left;
                } else {
                    target = target.right;
                }
            }
            if (target == null) return root;
            TreeNode temp = remove(parent, target);
            if (target == root) return temp;
            return root;
        }
        private TreeNode remove(TreeNode parent, TreeNode target) {
            if (target.left == null && target.right == null) {
                // 两边都空
                if (parent.left == target) parent.left = null;
                else parent.right = null;
                return null;
            } else if (! (target.left != null && target.right != null)) {
                // 有一个子树
                TreeNode child = target.left != null ? target.left : target.right;
                if (parent.left == target) parent.left = child;
                else parent.right = child;
                return child;
            } else {
                // 有两个子树
                // 找target的successor
                TreeNode next_parent = target;
                TreeNode next_target = target.right;
                while (next_target.left != null) {
                    next_parent = next_target;
                    next_target = next_target.left;
                }
                // 复制，替换
                TreeNode temp = new TreeNode(next_target.val);
                temp.left = target.left;
                temp.right = target.right;
                if (parent.left == target) {
                    parent.left = temp;
                } else {
                    parent.right = temp;
                }
                    // 重要一步，如果next_parent是之前的target， 也需要替换
                if (next_parent == target) next_parent = temp;
                remove(next_parent, next_target);
                return temp;
            }
        }
    }
```
<br>

---
_update Dec 25, 2017  20:28_

### Update
还是要建立分模块设计的思想。比如这道题，另外写两个函数 `findRightMin(), findNode()`，可以令主函数 `remove()` 更加易读，写的时候也方便。

实现的时候，先考虑target node没有孩子的情况，再考虑有两个孩子的情况，再考虑只有一个孩子的情况，这样写逻辑最简单。另外写一个 `remove(parent, node)` 是为了处理待删除点是 root 的情况。

Python实现见前面。

















