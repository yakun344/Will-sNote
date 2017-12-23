## Unique Binary Search Trees II
_update Aug 13, 2017  15:26_

---
[LeetCode](https://leetcode.com/problems/unique-binary-search-trees-ii/description/)

Given an integer n, generate all structurally unique BST's (binary search trees) that store values 1...n.

**For example**

Given n = 3, your program should return all 5 unique BST's shown below.

     1         3     3      2      1
      \       /     /      / \      \
       3     2     1      1   3      2
      /     /       \                 \
     2     1         2                 3
     
#### Basic Idea:
**首先明确一点**，很多时候求解的数量时用dp，但是求所有解的时候一定要用dfs。(用dp求此题解的数量的题目笔记在 [这里](https://will-gxz.gitbooks.io/xiaozheng_algo/content/dynamic-programming/unique-binary-search-trees.html))

所以这里首先考虑dfs的解法。我们可以定义一个`list<TreeNode> helper(int start, int end)`函数，这个函数的定义是：**返回一个list，包含[start, end]这个序列组成的所有BST**。我们利用这个函数，对于一个输入 n 即 [1,n]，我们可以另其内每个元素为root，然后生成其左右两子序列的所有BST，然后只要以其本身为root，将生成的左右子树们两两配对组装即可。

#### Python Code:
```python
    # Definition for a binary tree node.
    # class TreeNode(object):
    #     def __init__(self, x):
    #         self.val = x
    #         self.left = None
    #         self.right = None
    
    class Solution(object):
        def generateTrees(self, n):
            """
            :type n: int
            :rtype: List[TreeNode]
            """
            # defination:
            # 返回一个 list<TreeNode>, 其中包含 [start, end] 这段序列的全部BST的root
            def helper(start, end):
                if start > end:
                    return [None]
                # 以每个元素为root，分别生成左右全部组合，再组装
                ret = []
                for i in range(start, end + 1):
                    left = helper(start, i - 1)
                    right = helper(i + 1, end)
                    # 两两配对，组装
                    for l in left:
                        for r in right:
                            root = TreeNode(i)
                            root.left = l
                            root.right = r
                            ret.append(root)
                return ret
            
            if n == 0:
                return []
            return helper(1, n)
```

#### Java Code:
[出处](https://github.com/yuzhangcmu/LeetCode/blob/master/tree/GenerateTree2.java) 在这里，这段代码不是我原创。
```java
    /**
     * Definition for binary tree
     * public class TreeNode {
     *     int val;
     *     TreeNode left;
     *     TreeNode right;
     *     TreeNode(int x) { val = x; left = null; right = null; }
     * }
     */
    public class GenerateTree2 {
        public List<TreeNode> generateTrees(int n) {
            return generateTreesHelp(1, n);
        }
        
        /*
          使用递归来完成，我们可以分解为2个步骤：
          完成左子树，完成右子树。
          如果说左子树有n种组合，右子树有m种组合，那最终的组合数就是n*m. 把这所有的组合组装起来即可
        */    
        public List<TreeNode> generateTreesHelp(int start, int end) {
            ArrayList<TreeNode> ret = new ArrayList<TreeNode>();
    
            // null也是一种解，也需要把它加上去。这样在组装左右子树的时候，不会出现左边没有解的情况，或
            // 是右边没有解的情况
            if (start > end) {
                ret.add(null);
                return ret;
            }
    
            for (int i = start; i <= end; i++) {
                // 求出左右子树的所有的可能。
                List<TreeNode> left = generateTreesHelp(start, i - 1);
                List<TreeNode> right =  generateTreesHelp(i + 1, end);
    
                // 将左右子树的所有的可能性全部组装起来
                for (TreeNode l: left) {
                    for(TreeNode r: right) {
                        // 先创建根节点 
                        TreeNode root = new TreeNode(i);
                        root.left = l;
                        root.right = r;
    
                        // 将组合出来的树加到结果集合中。
                        ret.add(root);
                    }
                }
            }
    
            return ret;
        }
    }
```