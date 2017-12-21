## Binary Tree Maximum Path Sum
_update Aug 28, 2017  22:35_

---
[LeetCode](https://leetcode.com/problems/binary-tree-maximum-path-sum/description/)

Given a binary tree, find the maximum path sum.

For this problem, a path is defined as any sequence of nodes from some starting node to any node in the tree along the parent-child connections. The path must contain at least one node and does not need to go through the root.

**For example:**

       Given the below binary tree,
       
              1
             / \
            2   3
       Return 6.
       
#### Basic Idea:
这道题目和[Binary Tree Longest Consecutive Sequence II](https://will-gxz.gitbooks.io/xiaozheng_algo/content/Tree/binary-tree-longest-consecutive-sequence-ii.html)类似，都是尽管需要考虑倒"V"型路径，但是只需要保持一个变量（一个是最长连续序列，而这道题是最大和序列）。因此，我们可以采取和之前题目类似的方法，用一个 helper function, 这个函数功能如下:
-  接收一个TreeNode变量，返回以这个node作为起始点的自上而下的path的最大和。这里需要注意，当两子树的最大和都小于零时，则抛弃两子树，从当前node开始新的累和，即当前node的最大和为node.val;
-  在函数中，需要跟踪更新全局变量 maxSum，还要考虑以当前node为转折点的倒“V”型路径和；

最终，整个程序思路其实和求自上而下路径的最大和类似；

#### Java Code:
```java
    class Solution {
        private int maxSum;
        public int maxPathSum(TreeNode root) {
            if (root == null) return 0;
            maxSum = Integer.MIN_VALUE;
            helper(root);
            return maxSum;
        }
        private int helper(TreeNode root) {
            if (root == null) return 0;
            int left_max = helper(root.left);
            int right_max = helper(root.right);
            int vPathSum = left_max + right_max + root.val;
            int currMaxSum = Math.max(left_max, right_max) + root.val;
            // 如果两子树最大和都小于零，则从当前node开始重新计算累和
            if (currMaxSum < root.val) currMaxSum = root.val;
            maxSum = Math.max(maxSum, Math.max(vPathSum, currMaxSum));
            return currMaxSum;
        }
    }
```
#### Python Code:
```python
    class Solution(object):
        def maxPathSum(self, root):
            """
            :type root: TreeNode
            :rtype: int
            """
            def helper(root):
                if not root:
                    return 0
                left_max = helper(root.left)
                right_max = helper(root.right)
                v_path_sum = left_max + right_max + root.val
                currMaxSum = max(left_max, right_max) + root.val
                if currMaxSum < root.val:
                    currMaxSum = root.val
                self.maxSum = max(currMaxSum, v_path_sum, self.maxSum)
                return currMaxSum
                
                
            if not root:
                return 0
            self.maxSum = float('-inf')
            helper(root)
            return self.maxSum
```
<br>

---
_update Dec 21, 2017  2:19_

### Update
这次重做这道题后有一点新感悟：  
&emsp; 当我们需要得到从根向上的path时，我们选择使用函数的返回值，从对 左右子树 的调用中获取下方的信息。当我们需要从 root 往下的 path 时，我们使用传入 path 参数的方法。











