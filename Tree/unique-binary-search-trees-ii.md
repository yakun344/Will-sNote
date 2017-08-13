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
**首先明确一点**，很多时候求解的数量时用dp，但是求所有解的时候一定要用dfs。

所以这里首先考虑dfs的解法。我们可以定义一个`list<TreeNode> helper(int start, int end)`函数，这个函数的定义是：`返回一个list，包含[start, end]这个序列组成的所有BST`。我们利用这个函数，对于一个输入 n 即 [1,n]，我们可以另其内每个元素为root，然后生成其左右两子序列的所有BST，然后只要以其本身为root，将生成的左右子树们两两配对组装即可。

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

```