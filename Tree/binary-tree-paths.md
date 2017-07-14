## Binary Tree Paths
_update Jun 28, 2017_

---

[leetcode](https://leetcode.com/problems/binary-tree-paths/#/description)
Given a binary tree, return all root-to-leaf paths.

**For example**, 

    given the following binary tree:
      
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

---
_update Jul 14, 2017 14:17_
#### 更新：九章算法中的思路
学习了九章，他们强调对于这类题目有两种思路，分别是**traverse法和分治法**。**前者**比较类似之前的第一种python实现，利用全局变量res记录paths，然后随着遍历逐个node更新path，当走到leaf的时候把path加入res。**后者**则是利用同一个问题对于左右子树的结论去推导最终结论。对于这道题，先得到左右子树所有的paths，然后在他们前面加上当前node.val（用for loop）。

**java 实现：**
```java
    // traverse 法
    public List<String> binaryTreePaths(TreeNode root) {
        List<String> res = new ArrayList<>();
        if (root == null) return res;
        helper(root, "", res);
        for (int i = 0; i < res.size(); ++i) {
            res.set(i, res.get(i).substring(2, res.get(i).length()));
        }
        return res;
    }
    private void helper(TreeNode node, String path, List<String> res) {
        if (node.left == null && node.right == null) {
            res.add(path + "->" + node.val);
            return;
        }
        if (node.left != null) {
            helper(node.left, path + "->" + node.val, res);
        }
        if (node.right != null) {
            helper(node.right, path + "->" + node.val, res);
        }
    }
    
    // 分治 法
    public List<String> binaryTreePaths(TreeNode root) {
        if (root == null) return new ArrayList<String>();
        List<String> res = helper(root);
        return res;
    }
    private List<String> helper(TreeNode node) {
        List<String> res = new ArrayList<>();
        if (node == null) {
            return res;
        }
        List<String> left = helper(node.left);
        List<String> right = helper(node.right);
        res.addAll(left);
        res.addAll(right);
        if (res.size() == 0) {
            res.add(node.val + "");
        } else {
            for (int i = 0; i < res.size(); ++i) {
                res.set(i, node.val + "->" + res.get(i));
            }
        }
        return res;
    }
```