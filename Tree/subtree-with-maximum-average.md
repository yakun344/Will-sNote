## Subtree with Maximum Average
_update Jul 13, 2017 15:57_

---
[LintCode](http://www.lintcode.com/en/problem/subtree-with-maximum-average/#)

Given a binary tree, find the subtree with maximum average. Return the root of the subtree.

** Notice**

LintCode will print the subtree which root is your return node.
It's guaranteed that there is only one subtree with maximum average.

     
**Example**
Given a binary tree:
     
          1
        /   \
      -5     11
      / \   /  \
     1   2 4    -2 
return the node 11.

#### Basic Idea:
根据九章中给出的处理tree问题的思路，使用分治法和全局变量结合的方法更加容易。所谓分治法，**个人的理解就是**对于一个相对于root的大的问题，我们先看能否将其化解为关于两个 child 的小问题。状态的改变依赖于返回值而不是全局变量。对于**Java**，我们可以定义一个名为`ReturnType`的内部类作为helper函数的返回值。对于**python**，我们可以直接返回不止一个值（as tuple），所以不需定义多余的 class。

具体到这道题，要求平均值最大的 subtree，我们除了要知道sum之外还得知道node的个数，所以定义一个包含这两个值的 ReturnType inner class，再用一个 ReturnType result 代表全剧最优值，和一个 TreeNode 作为全局最优值对应的 root。

**Reference:** [九章笔记第三章](https://stomachache007.wordpress.com/2017/03/12/%E4%B9%9D%E7%AB%A0%E7%AE%97%E6%B3%95%E7%AC%94%E8%AE%B0-3-binary-tree-divide-conquer/)

#### Java Code:
```java
    public class Solution {
        /*
         * @param root: the root of binary tree
         * @return: the root of the maximum average of subtree
         */
        private class Tuple {
            int sum, count;
            Tuple(int sum, int count) {
                this.sum = sum;
                this.count = count;
            }
        }
        private int currSum;
        private int currCount;
        private TreeNode currRoot;
        public TreeNode findSubtree2(TreeNode root) {
            helper(root);
            return currRoot;
        }
        
        private Tuple helper(TreeNode root) {
            if (root == null) return new Tuple(0, 0);
            Tuple left = helper(root.left);
            Tuple right = helper(root.right);
            int sum = left.sum + right.sum + root.val;
            int count = left.count + right.count + 1;
            if (currRoot == null || currSum * count < currCount * sum) {
                currSum = sum;
                currCount = count;
                currRoot = root;
            }
            return new Tuple(sum, count);
        }
    }
```

#### Python Code:
因为python可以返回不同类型的不止一个值，就不必专门定义ReturnType class 了。
```python
    class Solution:
        # @param {TreeNode} root the root of binary tree
        # @return {TreeNode} the root of the maximum average of subtree
        
        sum, num, root = 0, 0, None
        
        def findSubtree2(self, root):
            def helper(node):
                """
                param: TreeNode node
                retype: int sum, int num
                """
                if not node:
                    return 0, 0
                lsum, lnum = helper(node.left)
                rsum, rnum = helper(node.right)
                sum = lsum + rsum + node.val
                num = lnum + rnum + 1
                if (not self.root) or (self.sum * num < sum * self.num):
                    self.sum = sum
                    self.num = num
                    self.root = node
                return sum, num
        
            helper(root)
            return self.root
```
