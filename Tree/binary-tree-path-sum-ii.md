## Binary Tree Path Sum II
_update Jul 14, 2017 20:14_

---
[lintcode](http://www.lintcode.com/en/problem/binary-tree-path-sum-ii/)  

Your are given a binary tree in which each node contains a value. Design an algorithm to get all paths which sum to a given value. The path does not need to start or end at the root or a leaf, but it must go in a straight line down.

**Example**

          Given a binary tree:

                  1
                 / \
                2   3
               /   /
              4   2
          for target = 6, return

              [
                [2, 4],
                [1, 3, 2]
              ]

#### Basic Idea:
这道题和leetcode上的 **[Path Sum III](https://willguo-private.gitbook.io/xiaozheng-algo/algorithm-problems/tree/path-sum-iii)** 其实很接近，但是这道题更加要求输出所有的路径。在前面的那篇文章中我们使用了两个函数两层递归的方式，但是这样的方法相对复杂。学习了九章算法之后，我发现我们可以使用一种**更加通用而且易于理解**的方法：  

整个代码的框架是一个求所有path并返回的程序，只是在我们到达每个node之后，对当前的path进行一次循环，考虑所有在当前位置结束的path，如果 sum == target ，则将这一段path加入res。具体地，对于Java，我们可以用 `res.add(new ArrayList<Integer>(path.subList(i, path.size())))` , 在Python中更加方便，直接`res.append(path[i:])`。

#### Java Code：
```java
    public class Solution {
        /**
         * @param root the root of binary tree
         * @param target an integer
         * @return all valid paths
         */

        // 先按照正常方法traverse，在path中跟踪路径，只是每次到新的node，for循环当前
        // path，找以此node结尾的path中是否有满足要求的，若有，加入res
        public List<List<Integer>> binaryTreePathSum2(TreeNode root, int target) {
            List<List<Integer>> res = new ArrayList<>();
            helper(root, new ArrayList<Integer>(), res, target);
            return res;
        }
        private void helper(TreeNode node, List<Integer> path, List<List<Integer>> res, int target) {
            if (node == null) return;
            path.add(node.val);
            for (int i = 0; i < path.size(); ++i) {
                int sum = 0;
                for (int j = i; j < path.size(); ++j) {
                    sum += path.get(j);
                }
                if (sum == target) {
                    res.add(new ArrayList<Integer>(path.subList(i, path.size())));
                }
            }
            helper(node.left, path, res, target);
            helper(node.right, path, res, target);
            path.remove(path.size() - 1);
        }    
    }
```

#### Python Code:
```python
    class Solution:
        # @param {TreeNode} root the root of binary tree
        # @param {int} target an integer
        # @return {int[][]} all valid paths
        def binaryTreePathSum2(self, root, target):
            def helper(root, path, res, target):
                if not root: return
                path.append(root.val)
                for i in range(len(path)):
                    sum = 0
                    for j in range(i, len(path)):
                        sum += path[j]
                    if sum == target:
                        res.append(path[i:])
                helper(root.left, path, res, target)
                helper(root.right, path, res, target)
                del path[-1]

            res = []
            helper(root, [], res, target)
            return res
```
<br>

---
_update Dec 21, 2017  23:36_

### Update
时隔数月，发现之前的写法其实并没有领会九章算法所授的**分治法**的道理。在这里重新写了一个新方法的 Java solution。

在这次的写法中，严格按照九章分治法的思路，对于一个关于root的问题，我们将其化解为一个关于 `root.left` 和 `root.right` 的同样的问题，再利用左右的返回值求出关于 root 的解。

具体地，我们通过左右子树递归的返回值得到从 左右节点 出发向下的所有路径，然后在每个这种路径的前面都加上 `root.val`，再加上一个只含有 `root.val` 的路径，我们就得到了从 root 开始向下的所有路径。这样就完成了一个分治算法。在得到从root开始向下的所有路径之后，我们循环判断这些路径中是否有满足条件的，若有，则将其副本加入 res;

```java
    public class Solution {
        /*
         * @param root: the root of binary tree
         * @param target: An integer
         * @return: all valid paths
         */
        // 从下往上
        public List<List<Integer>> binaryTreePathSum2(TreeNode root, int target) {
            List<List<Integer>> res = new ArrayList<>();
            if (root == null) return res;
            helper(root, target, res);
            return res;
        }

        // return all paths go down from this node, 每次检查从该 root 开始有无满足要求的path
        private List<List<Integer>> helper(TreeNode root, int target, List<List<Integer>> res) {
            List<List<Integer>> paths = new ArrayList<>();
            if (root == null) return paths;
            List<List<Integer>> leftPaths = helper(root.left, target, res);
            List<List<Integer>> rightPaths = helper(root.right, target, res);
            paths.addAll(leftPaths);
            paths.addAll(rightPaths);
            // 插入一个空链表，这样将root加在每个路径前面之后，这个链表就表示一条只有
            // root一个node的路径。这也是递归最先开始有记录的地方，就是left和right都是
            // 空，然后插入一个空链表，再其头部加上root，成为第一条路径.        
            paths.add(new LinkedList<Integer>());
            for (List<Integer> path : paths)  path.add(0, root.val);
            // 此时 paths 中就包含了所有从当前 root 开始向下的路径, 然后我们开始检查每
            // 条路径的和，如果满足要求，则把次path的副本加入res（之所以是副本，是因为随着
            // 递归的进行，该path还会被修改）
            for (List<Integer> path : paths) {
                int sum = 0;
                for (int n : path) sum += n;
                if (sum == target) res.add(new ArrayList<>(path));
            }
            return paths;
        }
    }
```

---
_update Feb 19 2019, 17:27_

### Update: 最直观的解
重做此题，感觉最直观最简单的解法任然是从上到下传一个path数组，每一层先用两层for loop计算有多少从上到下且以当前node截止的满足条件的path，加入res。
```java
public class Solution {
    /*
     * @param root: the root of binary tree
     * @param target: An integer
     * @return: all valid paths
     */
    public List<List<Integer>> binaryTreePathSum2(TreeNode root, int target) {
        List<List<Integer>> res = new ArrayList<>();
        if (root == null) return res;
        dfs(root, new ArrayList<>(), res, target);
        return res;
    }
    
    private void dfs(TreeNode root, List<Integer> path, List<List<Integer>> res, int target) {
        path.add(root.val);
        for (int i = 0; i < path.size(); ++i) {
            int sum = 0;
            for (int j = i; j < path.size(); ++j) {
                sum += path.get(j);
            }
            if (sum == target) res.add(new ArrayList<>(path.subList(i, path.size())));
        }
        if (root.left != null) dfs(root.left, path, res, target);
        if (root.right != null) dfs(root.right, path, res, target);
        path.remove(path.size() - 1);
    }
}
```
