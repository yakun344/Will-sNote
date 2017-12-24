## Inorder Successor in Binary Search Tree
_update Aug 28, 2017  17:50_

---
[LintCode](http://www.lintcode.com/en/problem/inorder-successor-in-binary-search-tree/)  
[LeetCode](https://leetcode.com/problems/inorder-successor-in-bst/description/)

Given a binary search tree (See Definition) and a node in it, find the in-order successor of that node in the BST.

If the given node has no in-order successor in the tree, return null.

**Notice**

It's guaranteed p is one node in the given tree. (You can directly compare the memory address to find p)

**Example**

    Given tree = [2,1] and node = 1:
    
              2
             /
            1
    return node 2.
    
    Given tree = [2,1,3] and node = 2:
    
              2
             / \
            1   3
    return node 3.

**Challenge **

O(h), where h is the height of the BST.

#### Basic Idea:
根据CLRS（算法导论）中的思想，找一个节点 p 的 successor 的最快方法如下：
-  如果 p 有右子树，则 successor 为其右子树中最小的元素，即 `p.right.leftMost`；
-  如果 p 没有右子树，则向 parent 方向一路向上看，successor 就是第一个右祖先，即p所在子树是其左子树；

这个算法的时间复杂度是 O(h)；

#### Java Code：
```java
    public class Solution {
        public TreeNode inorderSuccessor(TreeNode root, TreeNode p) {
            if (root == null || p == null) return null;
            Deque<TreeNode> stack = new LinkedList<>();
            // 如果p有右孩子，则successor一定在右子树中
            if (p.right != null) {
                TreeNode curr = p.right;
                while (curr.left != null) {
                    curr = curr.left;
                }
                return curr;
            }
            
            // 如果p没有右孩子，则找其祖先，先从root开始，找到p，沿途node压栈
            TreeNode curr = root;
            while (curr != p) {
                stack.addLast(curr);
                if (p.val < curr.val) {
                    curr = curr.left;
                } else {
                    curr = curr.right;
                }
            }
            // 此时curr==p，将前面的祖先逐一出栈，返回第一个右祖先
            while (! stack.isEmpty() && stack.peekLast().left != curr) {
                curr = stack.removeLast();
            }
            if (stack.isEmpty()) return null;
            return stack.peekLast();
        }
    }
```
#### Python Code:
```python
    class Solution(object):
        def inorderSuccessor(self, root, p):
            """
            :type root: TreeNode
            :type p: TreeNode
            :rtype: TreeNode
            """
            if not root or not p:
                return None
            # 考虑右子树
            if p.right:
                p = p.right
                while p.left:
                    p = p.left
                return p
            # 考虑祖先
            stack = []
            curr = root
            while curr != p:
                stack.append(curr)
                if p.val < curr.val:
                    curr = curr.left
                else:
                    curr = curr.right
            # 此时 curr==p
            while stack and stack[-1].left != curr:
                curr = stack.pop()
            if not stack:
                return None
            return stack[-1]
```

    