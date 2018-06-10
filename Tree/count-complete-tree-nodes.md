## Count Complete Tree Nodes
_update Aug 31, 2017  18:44_

---
[LeetCode](https://leetcode.com/problems/count-complete-tree-nodes/description/)

Given a complete binary tree, count the number of nodes.

**Definition of a complete binary tree from Wikipedia:**
In a complete binary tree every level, except possibly the last, is completely filled, and all nodes in the last level are as far left as possible. It can have between 1 and 2h nodes inclusive at the last level h.

#### Basic Idea:
[这里](http://www.cnblogs.com/grandyang/p/4567827.html) 是一个很不错的分析；

利用完全二叉树的性质，我们知道：
-  要得到一颗 complete 二叉树的最大高度，只要沿着最左边一路向下即可；
-  如果左右两子树的高度相等，可以肯定左子树是满二叉树，即可通过 `n=2^h-1` 计算出左子树的节点个数；

由上面的思路，我们可以得到一种算法，每次对于当前root，计算左右subtree的高度，如果高度相同，则算出左子树node个数，向右递归，如果不同，则向两方向递归。最终结果就是 `left_num + right_num + 1`；

时间复杂度：这个算法每次计算高度耗时 O(logN)，而一共需要计算O(logN)次，所以总时间复杂度为 `O( (logN)^2 )`;

#### Python Code:
```python
    class Solution:
        def countNodes(self, root):
            """
            :type root: TreeNode
            :rtype: int
            """
            def getHight(root):
                if not root: return 0
                leftHight = getHight(root.left)
                return leftHight + 1


            if not root: return 0
            leftHight = getHight(root.left)
            rightHight = getHight(root.right)
            if leftHight == rightHight:
                return 2 ** (leftHight) + self.countNodes(root.right)
            else:
                return self.countNodes(root.left) + self.countNodes(root.right) + 1
```

---

_update 2018-06-10 00:15:26_     

### Update C++ Solution
更快的做法是每次都 explore 极左和极右两条 path 的 depth，如果两者相同，则可以直接返回 `2 ^ (depth + 1) - 1`，否则的话说明当前root不是满二叉树，则需要继续向下explore，则返回 `1 + count(root->left) + count(root->right)`；

```cpp
class Solution {
public:
    int countNodes(TreeNode* root) {
        if (! root) return 0;
        int left = 0, right = 0;
        TreeNode* curr = root;
        while (curr->left) {
            ++left;
            curr = curr->left;
        }
        curr = root;
        while (curr->right) {
            ++right;
            curr = curr->right;
        }
        if (left == right) return pow(2, left + 1) - 1;
        else return 1 + countNodes(root->left) + countNodes(root->right);
    }
};
```
