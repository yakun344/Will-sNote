## Count Univalue Subtrees
_update Sep,1 2017  19:28_

---
[LeetCode](https://leetcode.com/problems/count-univalue-subtrees/description/)

Given a binary tree, count the number of uni-value subtrees.

A Uni-value subtree means all nodes of the subtree have the same value.

**For example:**

              Given binary tree,
                            5
                           / \
                          1   5
                         / \   \
                        5   5   5
              return 4.


#### Basic Idea:
使用分治的思路，维持一个 int count 全局变量，写一个 `boolean helper(TreeNode root)`，实现：
-  如果当前root的tree符合要求，返回True，count++；
-  如果当前root的左右两子树都返回True，并且左右child的值为null或和root相同，则说明当前root所在树符合要求；

利用这个思路，可以轻松写出recursive function；

#### Python Code:
注意判断逻辑，这道题中排除不符合条件的情况比较简便；
```python
    class Solution(object):
        def countUnivalSubtrees(self, root):
            """
            :type root: TreeNode
            :rtype: int
            """
            # 如果当前tree符合条件，则返回true，同时 self.count++
            # 如果左右都是，并且值符合，则当前树符合
            def helper(root):
                if not root:
                    return True
                left = helper(root.left)
                right = helper(root.right)
                # 接下来的判断逻辑很关键，这样写最简单，排除不满足情况的，剩下就是True
                if left and right:
                    if root.left and root.left.val != root.val:
                        return False
                    if root.right and root.right.val != root.val:
                        return False
                    self.count += 1
                    return True
                else:  
                    return False
                
            self.count = 0
            helper(root)
            return self.count
```
如果列举满足条件的情况，见下方python code，注意对比：
```python
class Solution:
    def countUnivalSubtrees(self, root):
        """
        :type root: TreeNode
        :rtype: int
        """
        def helper(root):
            if not root: return True
            left = helper(root.left)
            right = helper(root.right)
            if left and right:
            # 这样写就冗杂很多
                if root.left and root.right:
                    if root.left.val == root.right.val == root.val:
                        self.ret += 1
                        return True
                elif not root.left and not root.right:
                    self.ret += 1
                    return True
                elif root.left:
                    if root.left.val == root.val:
                        self.ret += 1
                        return True
                else:
                    if root.right.val == root.val:
                        self.ret += 1
                        return True
            return False
            
        
        self.ret = 0
        helper(root)
        return self.ret
```          
            