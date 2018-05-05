## Minimum Depth of Binary Tree
_update Jan 20,2018  10:24_

---
[LeetCode](https://leetcode.com/problems/minimum-depth-of-binary-tree/description/)

Given a binary tree, find its minimum depth.

The minimum depth is the number of nodes along the shortest path from the root node down to the nearest leaf node.

<br>

### Basic Idea:
这道题目的**陷阱**是要判断一个node是child需要判断他左右子树都为null，此时才能得到他的height。

**两种思路： ** DFS 和 BFS。我认为 BFS 会比较好，因为BFS一定会先找到 minimum depth 之后就可以直接返回，而 DFS 需要搜索整个二叉树。

### DFS Solution:
* #### Java
```java
class Solution {
    public int minDepth(TreeNode root) {
        if (root == null) return 0;
        else if (root.left == null && root.right == null) {
            return 1;
        } else if (root.left == null) {
            return minDepth(root.right) + 1;
        } else if (root.right == null) {
            return minDepth(root.left) + 1;
        } else {
            return Math.min(minDepth(root.left), minDepth(root.right)) + 1;
        }
    }
}
```

### BFS Solution:
* #### Java
```java
class Solution {
    public int minDepth(TreeNode root) {
        if (root == null) return 0;
        Deque<TreeNode> queue = new ArrayDeque<>();
        int depth = 0;
        queue.offerFirst(root);
        while (! queue.isEmpty()) {
            ++depth;
            int size = queue.size();
            for (int i = 0; i < size; ++i) {
                TreeNode node = queue.pollLast();
                if (node.left == null && node.right == null) {
                    return depth;
                } 
                if (node.left != null) {
                    queue.offerFirst(node.left);
                }
                if (node.right != null) {
                    queue.offerFirst(node.right);
                }
            }
        }
        return depth; // 不可以没有： missing return statement
    }
}
```

* #### C++
```cpp
class Solution {
public:
    int minDepth(TreeNode* root) {
        if (root == nullptr) return 0;
        deque<TreeNode*> dq;
        dq.push_back(root);
        int depth{0};
        while (! dq.empty()) {
            ++depth;
            int size = dq.size();
            for (int i = 0; i < size; ++i) {
                TreeNode* node = dq.front();
                dq.pop_front();
                if (node->left == nullptr && node->right == nullptr) {
                    return depth;
                }
                if (node->left) {
                    dq.push_back(node->left);
                } 
                if (node->right) {
                    dq.push_back(node->right);
                }
            }
        }
        // return depth; // 可以有，可以没有
    }
};
```

