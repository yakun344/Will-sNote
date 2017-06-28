## Binary Tree Paths
_update Jun 28, 2017_

---

[leetcode](https://leetcode.com/problems/binary-tree-paths/#/description)
Given a binary tree, return all root-to-leaf paths.

For example, given the following binary tree:
      
         1
       /   \
      2     3
       \
        5
All root-to-leaf paths are:

    ["1->2->5", "1->3"]
   
   
#### 分析
这道题目应该很经典，要输出所有的path，我们就需要在每层recursion中传入当前node之前的路径，也就是path。明白了这一点之后，这个问题几乎就变成了简单的dfs或者bfs。在这里，我记录三种实现，分别是recursive dfs，dfs with stack and bfs with queue。

##### Recursive DFS
Python Code：
```python
    class Solution(object):
        res = []
        def binaryTreePaths(self, root):
            """
            :type root: TreeNode
            :rtype: List[str]
            """
            def dfs(node, path): #path is a [string]
                if not node: return
                if not node.left and not node.right:
                    self.res.append(path + str(node.val))
                path += str(node.val) + '->'
                if node.left:
                    dfs(node.left, path[:])
                if node.right:
                    dfs(node.right, path[:])
                    
            if root is None:
                return self.res
            dfs(root, '')
            ret = self.res[:]
            del self.res[:]
            return ret
```

##### DFS with Stack
Python Code:
```python
    class Solution(object):
        def binaryTreePaths(self, root):
            """
            :type root: TreeNode
            :rtype: List[str]
            """
            if not root:
                return []
            res, stack = [], [(root, '')] # python 的list可以轻松存放tuple，如果是java则需要使用两个stack
            while stack:
                node, path = stack.pop()
                if not node.left and not node.right:
                    res.append(path + str(node.val))
                if node.left:
                    stack.append((node.left, path + str(node.val) + '->'))
                if node.right:
                    stack.append((node.right, path + str(node.val) + '->'))
            return res
```

##### BFS with Queue
```python
    class Solution(object):
        def binaryTreePaths(self, root):
            """
            :type root: TreeNode
            :rtype: List[str]
            """
            if root is None:
                return []
            res, queue = [], collections.deque([(root, '')])
            while queue:
                node, path = queue.popleft()
                if not node.left and not node.right:
                    res.append(path + str(node.val))
                if node.left:
                    queue.append((node.left, path + str(node.val) + '->'))
                if node.right:
                    queue.append((node.right, path + str(node.val) + '->'))
            return res
```