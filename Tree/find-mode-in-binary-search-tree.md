## Find Mode in Binary Search Tree
_update Jun 27, 2017_

---
[leetcode](https://leetcode.com/problems/find-mode-in-binary-search-tree/#/description)
Given a binary search tree (BST) with duplicates, find all the mode(s) (the most frequently occurred element) in the given BST.

Assume a BST is defined as follows:

The left subtree of a node contains only nodes with keys less than or equal to the node's key.
The right subtree of a node contains only nodes with keys greater than or equal to the node's key.
Both the left and right subtrees must also be binary search trees.
For example:
 Given BST [1,null,2,2]
 
         1
           \
             2
           /
         2

return [2].

Note: If a tree has more than one mode, you can return them in any order.

_Follow up_: Could you do that without using any extra space? (Assume that the implicit stack space incurred due to recursion does not count).

#### 思路
这道题的难点是不可以使用 counting map 暴力统计每个数字的频数，所以我们只有利用BST的性质，用 inorder traverse 把问题化简为一个求 sorted array 中最大频数的 element 的问题。我们可以 maintain 一个 `count`和一个`max`，同时跟踪`prev node`，如果`curr node.val == prev node.val`，我们另`count++`，如果`count == max`或者 `count > max`，我们再做出相应的操作。结果存在一个`list`中。

Java recursive code：
```java
    // java
    public class Solution {
        int max = -1;
        int count = -1;
        int prev = Integer.MAX_VALUE;
        public int[] findMode(TreeNode root) {
            if (root == null) return new int[] {};
            List<Integer> lst = new ArrayList<>();
            inOrder(root, lst);
            int[] ret = new int[lst.size()];
            for (int i = 0; i < ret.length; ++i) {
                ret[i] = lst.get(i);
            }
            return ret;
        }
        private void inOrder(TreeNode node, List<Integer> lst) {
            if (node == null) return;
            inOrder(node.left, lst);
            if (prev == node.val) {
                // curr == prev，则count++
                count++;
            } else {
                count = 1;
                prev = node.val;
            }
            if (count > max) {
            // 说明之前的max太小，所以清空list重新添加最大频率元素
                lst.clear();
                lst.add(node.val);
                max = count;
            } else if (count == max) {
                lst.add(node.val);
            }
            inOrder(node.right, lst);
        }
    }
```