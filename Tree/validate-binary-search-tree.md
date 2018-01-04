## Validate Binary Search Tree
_update Jul 14, 2017 18:13_

---
[LintCode](http://www.lintcode.com/en/problem/validate-binary-search-tree/)  
[LeetCode](https://leetcode.com/problems/validate-binary-search-tree/description/)

Given a binary tree, determine if it is a valid binary search tree (BST).

Assume a BST is **defined** as follows:

The left subtree of a node contains only nodes with keys less than the node's key.
The right subtree of a node contains only nodes with keys greater than the node's key.
Both the left and right subtrees must also be binary search trees.
A single node tree is a BST

**Example:**

            An example:
    
              2
             / \
            1   4
               / \
              3   5
    The above binary tree is serialized as {2,1,4,#,#,3,5} (in level order).
    
#### Basic Idea:
利用定义，每一个子树都需要是vlaid bst。对于每个root，需要保证root.val 大于左子树中的最大值，小于右子树的最小值。由此，我们可以定义一个ReturnType，其中包含`boolean isValid, max, min`三个变量。  
（需要注意的是，当node的值是Integer.MAX_VALUE的时候，会造成边界判定不准，可以转成(long)Integer.MAX_VALUE + 1之类来处理）

#### Java Code:
```java
    public class Solution {
        /**
         * @param root: The root of binary tree.
         * @return: True if the binary tree is BST, or false
         */
        // 使用分治法
        private class Return {
            boolean isValid = true;
            long min = Integer.MAX_VALUE;
            long max = Integer.MIN_VALUE; 
            public Return(boolean b, long min, long max) {
                isValid = b;
                this.min = min;
                this.max = max;
            }
        }
        public boolean isValidBST(TreeNode root) {
            if (root == null) return true;
            Return ret = helper(root);
            return ret.isValid;
        }
        private Return helper(TreeNode root) {
            if (root == null) return new Return(true, (long)Integer.MAX_VALUE + 1, (long)Integer.MIN_VALUE - 1);
            if (root.left == null && root.right == null) {
                return new Return(true, root.val, root.val);
            } 
            Return left = helper(root.left);
            Return right = helper(root.right);
            boolean flag = true;
            long max = Math.max(left.max, Math.max(right.max, root.val));
            long min = Math.min(left.min, Math.min(right.min, root.val));
            if (! left.isValid || ! right.isValid || root.val >= right.min || root.val <= left.max) {
                flag = false;
            }
            return new Return(flag, min, max);
        }
    }
```

---
_update Aug 27, 2017  20:39_

更新一个Python的解法，和前面Java的思路类似，但是这次我们不使用boolean flag，而是当我们检测到当前树invalid时，直接返回None；
另外还有一种思路，最简单的，就是inorder遍历一遍，然后验证生成的数组是否是递增的，但是这种方法有局限，就是无法判断如下情况：

         1                   1
       /   \     ---------     \
      1     1                   1
                                  \
                                   1
      有相同的 inorderList: [1,1,1]
但因为此题规定二叉树左右和root一定不相等，所以不用考虑这种情况，所以inorder traverse 是可以用的。
            
 #### Python:
 ```python
     class Solution(object):
        def isValidBST(self, root):
            # 返回（min, max），如果检查出已经invalid，返回None
            def helper(root):
                if not root:
                    return (float('inf'), float('-inf'))
                left = helper(root.left)
                right = helper(root.right)
                if not left or not right:
                    return None
                if left[1] >= root.val or right[0] <= root.val:
                    return None
                return (min(left[0], right[0], root.val), max(left[1], right[1], root.val))
            
            return helper(root) is not None
```
<br>

---
_update Dec 20, 2017 15:30_

### Update
更新一种解法，返回值只用于判断左右子树是否为valid，而找 leftMax 和 rightMin 依靠每层递归中的 while 循环；

**Java Code**
```java
    public class Solution {
        /*
         * @param root: The root of binary tree.
         * @return: True if the binary tree is BST, or false
         */
        public boolean isValidBST(TreeNode root) {
            if (root == null) return true;
            if (! isValidBST(root.left) || ! isValidBST(root.right)) {
                return false;
            }
            long leftMax;
            long rightMin;
            
            TreeNode node = root.left;
            while (node != null && node.right != null) node = node.right;
            leftMax = node != null ? node.val : (long)Integer.MIN_VALUE - 1;
            
            node = root.right;
            while (node != null && node.left != null) node = node.left;
            rightMin = node != null ? node.val : (long)Integer.MAX_VALUE + 1;
            
            if (root.val > leftMax && root.val < rightMin) return true;
            else return false;
        }
    }
```
<br>
再更新一种 iterative 的解法，就是简单的 inorder traversal，检查是否严格单调递增，对于这种没有重复元素的BST很好用。
```python
    # inorder traversal, check if is increasing
    class Solution:
        def isValidBST(self, root):
            """
            :type root: TreeNode
            :rtype: bool
            """
            if not root: return True
            stack = []
            
            while root:
                stack.append(root)
                root = root.left
                
            prev = float('-inf') # 跟踪前一个元素，初始化为负无穷
            while stack:
                node = stack.pop()
                if node.val <= prev: # 说明不是递增，则不是BST
                    return False
                prev = node.val # 更新prev
                node = node.right
                while node:
                    stack.append(node)
                    node = node.left
            return True
```
<br>

---
_update Jan,4 2017  15:36_

### Update
#### 更新一种解法，每次递归时传入两个边界
这种解法有些像 alpha-beta pruning：

1.  递归传入两个另外参数，alpha 为 lower bound，beta 为 upper bound，初始化分别为 `-inf` 和 `inf`；
2.  每次进入当前node，判断当前 node.val 是否在 alpha 和 beta 之间；
3.  每次对于当前 node，进入下层递归时，如果进入 node.left, 则更新 beta 为 `node.val`，若进入 node.right, 则更新 alpha 为 `node.val`；

这种解法的 code 最为简单，逻辑也很清晰，个人认为目前而言这种方法是最好的；

**Java Code:**  
```java
class Solution {
    public boolean isValidBST(TreeNode root) {
        return helper(root, (long)Integer.MIN_VALUE - 1, (long)Integer.MAX_VALUE + 1);
    }
    
    // 每次检查当前root.val是否在 alpha和beta之间，alpha为lower bound， beta 为 upper bound
    private boolean helper(TreeNode root, long alpha, long beta) {
        if (root == null) return true;
        if (root.val <= alpha || root.val >= beta) return false;
        return helper(root.left, alpha, root.val) && helper(root.right, root.val, beta);
    }
}
```

























