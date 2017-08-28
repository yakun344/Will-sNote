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

#### Recursive DFS
Python Code：
```python
    # dfs recursive
    class Solution(object):
        def binaryTreePaths(self, root):
            """
            :type root: TreeNode
            :rtype: List[str]
            """
            def dfs(root, path):
                if root is None:
                    return
                if root.left is None and root.right is None:
                    res.append(path + str(root.val))
                    return
                if root.left:
                    dfs(root.left, path + str(root.val) + '->')
                if root.right:
                    dfs(root.right, path + str(root.val) + '->')
                    
            res = []
            dfs(root, '')
            return res
```

#### DFS with Stack
**Python Code:**
```python
    class Solution(object):
        def binaryTreePaths(self, root):
            """
            :type root: TreeNode
            :rtype: List[str]
            """
            if not root:
                return []
            res, stack = [], [(root, '')] # python 的list可以轻松存放tuple，如果是java则需要使用两个stack,或者用内部类
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
**Java Code**
```java
    // using stack
    private class Path { // inner class
        String path;
        TreeNode node;
        public Path(String path, TreeNode node) {
            this.path = path;
            this.node = node;
        }
    }
    public List<String> binaryTreePaths(TreeNode root) {
        Deque<Path> stack = new LinkedList<>(); // stack of inner class
        List<String> res = new ArrayList<>();
        if (root == null) return res;
        stack.addLast(new Path(root.val + "", root));
        while (stack.size() > 0) {
            TreeNode node = stack.peekLast().node;
            String path = stack.peekLast().path;
            stack.removeLast();
            if (node.left == null && node.right == null) {
                res.add(path);
                continue;
            }
            if (node.left != null) {
                stack.addLast(new Path(path + "->" + node.left.val, node.left));
            }
            if (node.right != null) {
                stack.addLast(new Path(path + "->" + node.right.val, node.right));
            }
        }
        return res;
    }
```

#### BFS with Queue
**Python code:**
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
     class Solution {
        public List<String> binaryTreePaths(TreeNode root) {
            List<String> res = new ArrayList<>();
            dfs(root, "", res);
            return res;
        }
        private void dfs(TreeNode root, String path, List<String> res) {
            if (root == null) return;
            if (root.left == null && root.right == null) {
                res.add(path + root.val);
                return;
            }
            dfs(root.left, path + root.val + "->", res);
            dfs(root.right, path + root.val + "->", res);
        }
    }
    
  // 分治 法
    class Solution {
        public List<String> binaryTreePaths(TreeNode root) {
            List<String> res = new ArrayList<>();
            if (root == null) return res;
            if (root.left == null && root.right == null) {
                res.add("" + root.val);
                return res;
            }
            res.addAll(binaryTreePaths(root.left));
            res.addAll(binaryTreePaths(root.right));
            for (int i = 0; i < res.size(); ++i) {
                res.set(i, root.val + "->" + res.get(i));
            }
            return res;
        }
    }
```