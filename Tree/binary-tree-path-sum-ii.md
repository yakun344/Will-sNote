## Binary Tree Path Sum II
_update Jul 14, 2017 20:14_

---
[lintcode](http://www.lintcode.com/en/problem/binary-tree-path-sum-ii/)   
[LeetCode](https://leetcode.com/problems/path-sum-iii/description/) (类似，只要求输出路径个数)  

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
这道题和leetcode上的 **[Path Sum III](https://will-gxz.gitbooks.io/xiaozheng_algo/content/Tree/Path%20Sum%20III.html)** 其实很接近，但是这道题更加要求输出所有的路径。在前面的那篇文章中我们使用了两个函数两层递归的方式，但是这样的方法相对复杂。学习了九章算法之后，我发现我们可以使用一种**更加通用而且易于理解**的方法：
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