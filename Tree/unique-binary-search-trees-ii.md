## Unique Binary Search Trees II
_update Aug 13, 2017  15:26_
_update Dec 23, 2017  0:26_

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

&emsp; 所以这里首先考虑dfs的解法。我们可以定义一个`list<TreeNode> helper(int start, int end)`函数，这个函数的定义是：**返回一个list，包含[start, end]这个序列组成的所有BST**。我们利用这个函数，对于一个输入 n 即 [1,n]，我们可以令其内每个元素为root，然后生成其左右两子序列的所有BST，然后只要以其本身为root，将生成的左右子树们两两配对组装即可。

**时间复杂度分析：**   
&emsp;


**空间复杂度分析：**
&emsp;

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
```java
    class Solution {
        public List<TreeNode> generateTrees(int n) {
            if (n == 0) return new ArrayList<TreeNode>();
            return helper(1, n);
        }

        // 返回所有start到end部分的BST的root
        private List<TreeNode> helper(int start, int end) {
            List<TreeNode> ret = new ArrayList<>();
            if (start > end) {
                ret.add(null);
                return ret;
            }
            if (start == end) {
                ret.add(new TreeNode(start));
                return ret;
            }
            // 考虑 start到end 之间每个数字做root的情况
            for (int i = start; i <= end; ++i) {
                List<TreeNode> leftRoots = helper(start, i - 1);
                List<TreeNode> rightRoots = helper(i + 1, end);
                // 将左右的树穷举配对，以 i 为root 组成新的 tree，把 i 加入 ret
                for (TreeNode leftRoot : leftRoots) {
                    for (TreeNode rightRoot : rightRoots) {
                        TreeNode root = new TreeNode(i);
                        root.left = leftRoot;
                        root.right = rightRoot;
                        ret.add(root);
                    }
                }
            }
            return ret;
        }
    }
```

---
_update 2018-06-03 11:43:40_

#### C++ Code:
```cpp
class Solution {
    vector<TreeNode*> helper(int left, int right) {
        vector<TreeNode*> ret;
        if (left > right) return ret;
        else if (left == right) {
            ret.push_back(new TreeNode(left));
            return ret;
        }
        for (int i = left; i <= right; ++i) {
            vector<TreeNode*> leftRoots = helper(left, i - 1);
            vector<TreeNode*> rightRoots = helper(i + 1, right);
            // 如果一边为空，为让循环继续，需要插入一个null
            if (leftRoots.empty()) leftRoots.push_back(nullptr);
            if (rightRoots.empty()) rightRoots.push_back(nullptr);
            for (TreeNode* leftRoot : leftRoots) {
                for (TreeNode* rightRoot : rightRoots) {
                    TreeNode* root = new TreeNode(i);
                    root->left = leftRoot;
                    root->right = rightRoot;
                    ret.push_back(root);
                }
            }
        }
        return ret;
    }
public:
    vector<TreeNode*> generateTrees(int n) {
        return helper(1, n);
    }
};
```
