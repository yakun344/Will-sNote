# Construct Binary Tree from Preorder and Inorder Traversal

_update Aug 29, 2017 23:07_

[LeetCode](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/description//)

Given preorder and inorder traversal of a tree, construct the binary tree.

**Note:**  
You may assume that duplicates do not exist in the tree.

### Basic Idea:

通过观察，我们可以发现 preorder 遍历的结果的第一个数一定是 root，而在 inorder list 中，root 会把左右两子树一分为二，利用这种性质，我们可以得到一种递归解法：

* 对于输入inorder lst 和 preorder lst，先标记 preorde\[0\] 作为 root，然后在 inorder 中查找 root 的位置；
* 这样就将两个 list 分为了两部分，分表为当前root左右子树的元素，对左右子树递归执行；

例如，对于input：

```text
preorder: [1,2,4,5,7,3,6]
inorder:  [4,2,7,5,1,3,6]
```

我们可以看到，对于以 1 为 root 的树，我们可以用 1 将 inorder 分成两部分，递归传给下层函数的参数分别为：

```text
left:                        right:
    preorder: [2,3,5,7]            preorder: [3,6]
    inorder   [4,2,7,5]            inorder:  [3,6]
```

### Java Code:

```java
    class Solution {
        public TreeNode buildTree(int[] preorder, int[] inorder) {
            return helper(preorder, inorder);
        }
        private TreeNode helper(int[] preorder, int[] inorder) {
            if (preorder.length == 0 ) return null;
            if (preorder.length == 1) return new TreeNode(preorder[0]);
            TreeNode root = new TreeNode(preorder[0]);
            int root_idx = indexOf(inorder, preorder[0]);
            int l_subtree_size = root_idx;
            int r_subtree_size = inorder.length - root_idx - 1;
            if (l_subtree_size > 0) {
                root.left = helper(Arrays.copyOfRange(preorder, 1, l_subtree_size + 1), Arrays.copyOfRange(inorder, 0, root_idx));
            }
            if (r_subtree_size > 0) {
                root.right = helper(Arrays.copyOfRange(preorder, l_subtree_size + 1, preorder.length),
                                    Arrays.copyOfRange(inorder, root_idx + 1, inorder.length));
            }
            return root;
        }
        private int indexOf(int[] arr, int target) {
            for (int i = 0; i < arr.length; ++i) {
                if (arr[i] == target) return i;
            }
            return -1;
        }
    }
```

### Python Code:

```python
    class Solution(object):
        def buildTree(self, preorder, inorder):
            """
            :type preorder: List[int]
            :type inorder: List[int]
            :rtype: TreeNode
            """
            if not preorder:
                return None
            if len(preorder) == 1:
                return TreeNode(preorder[0])
            root = TreeNode(preorder[0])
            root_idx = inorder.index(preorder[0])
            left_size = root_idx
            right_size = len(inorder) - root_idx - 1
            if left_size:
                root.left = self.buildTree(preorder[1 : left_size + 1], inorder[0 : root_idx])
            if right_size:
                root.right = self.buildTree(preorder[left_size + 1 :], inorder[root_idx + 1 :])
            return root
```

_update Dec 24, 2017 3:01_

## Update:

**时间复杂度：** 因为我们每次在inorder list中找root位置时候都要线性搜索，时间复杂度为 O\(n^2\)，可以预处理一下，用一个 map 存放所有 node 在 inorder list 中的 index，这样时间复杂度变为 O\(n\)，但空间复杂度从 O\(1\) 变为 O\(n\);

**更新一个优化了时间复杂度的 Java 实现：**

```java
    class Solution {
        private Map<Integer, Integer> map;
        public TreeNode buildTree(int[] preorder, int[] inorder) {
            this.map = new HashMap<>();
            for (int idx = 0; idx < inorder.length; ++idx) {
                map.put(inorder[idx], idx);
            }
            return helper(preorder, inorder, 0, preorder.length - 1, 0, inorder.length - 1);
        }

        // return 为建成的tree的root，parameter是用来模拟传入了两个subarray`
        private TreeNode helper(int[] preorder, int[] inorder, int pstart, int pend, int istart, int iend) {
            if (pstart > pend) return null;
            TreeNode root = new TreeNode(preorder[pstart]);
            if (pstart == pend) return root;
            int rootIndex = map.get(root.val);
            int leftSize = rootIndex - istart;
            // 这里主要是index各种计算，看不懂的话可以画图举例理解
            root.left = helper(preorder, inorder, pstart + 1, pstart + leftSize, istart, rootIndex - 1);
            root.right = helper(preorder, inorder, pstart + leftSize + 1, pend, rootIndex + 1, iend);
            return root;
        }
    }
```

_update 2018-06-06 22:41:13_

## Update C++ Solution

运用之前的思路，用C++写了一个解法：

```cpp
class Solution {
    TreeNode* helper(unordered_map<int, int>& idxs, const vector<int>& preorder,
                     const vector<int>& inorder, int pre_start, int pre_end, int in_start, int in_end) {
        if (pre_start > pre_end || in_start > in_end) return nullptr;
        int root_val = preorder[pre_start];
        int root_inorder_idx = idxs[root_val];
        int leftSize = root_inorder_idx - in_start;
        int rightSize = in_end - root_inorder_idx;

        TreeNode* root = new TreeNode(root_val);
        root->left = helper(idxs, preorder, inorder, pre_start + 1, pre_start + leftSize, in_start, root_inorder_idx - 1);
        root->right = helper(idxs, preorder, inorder, pre_start + leftSize + 1, pre_end, root_inorder_idx + 1, in_end);

        return root;
    }
public:
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        if (preorder.empty()) return nullptr;
        unordered_map<int, int> idxs;
        for (int i = 0; i < inorder.size(); ++i) {
            idxs.insert(make_pair(inorder[i], i));
        }
        int size = preorder.size();
        return helper(idxs, preorder, inorder, 0, size - 1, 0, size - 1);
    }
};
```

