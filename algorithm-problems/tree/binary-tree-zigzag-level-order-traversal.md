# Binary Tree Zigzag Level Order Traversal

_update Aug 31, 2017 15:54_

[LeetCode](https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal/description/)

Given a binary tree, return the zigzag level order traversal of its nodes' values. \(ie, from left to right, then right to left for the next level and alternate between\).

**For example:**

```text
  Given binary tree [3,9,20,null,null,15,7],
      3
     / \
    9  20
      /  \
     15   7
  return its zigzag level order traversal as:
  [
    [3],
    [20,9],
    [15,7]
  ]
```

## Basic Idea:

Level order traverse，但是按照 “left-right”，“right-left” ,... 的顺序进行。解法非常简单，只要正常按照从左到右的方向进行，另外维持一个boolean标识当前的方向，如果是右向左，则把当前 level 的 vals 反向之后再加入 res；

## Java Code:

```java
    class Solution {
        public List<List<Integer>> zigzagLevelOrder(TreeNode root) {
            List<List<Integer>> res = new ArrayList<>();
            if (root == null) return res;
            Deque<TreeNode> queue = new LinkedList<>();

            boolean leftToRight = true;
            queue.addFirst(root);
            while (! queue.isEmpty()) {
                int size = queue.size();
                List<Integer> level = new ArrayList<>();
                for (int i = 0; i < size; ++i) {
                    TreeNode node = queue.removeLast();
                    level.add(node.val);
                    if (node.left != null) queue.addFirst(node.left);
                    if (node.right != null) queue.addFirst(node.right);
                }
                if (! leftToRight) Collections.reverse(level);
                res.add(level);
                leftToRight ^= true;
            }
            return res;
        }
    }
```

