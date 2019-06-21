# Maximum Width of Binary Tree

_update Sep 16 2018, 16:28_

[LeetCode](https://leetcode.com/problems/maximum-width-of-binary-tree/description/)

Given a binary tree, write a function to get the maximum width of the given tree. The width of a tree is the maximum width among all levels. The binary tree has the same structure as a full binary tree, but some nodes are null.

The width of one level is defined as the length between the end-nodes \(the leftmost and right most non-null nodes in the level, where the null nodes between the end-nodes are also counted into the length calculation.

**Example 1:**

Input:

```text
       1
     /   \
    3     2
   / \     \  
  5   3     9
```

Output: 4  
Explanation: The maximum width existing in the third level with the length 4 \(5,3,null,9\).

**Example 2:**

Input:

```text
      1
     /  
    3    
   / \       
  5   3     
```

Output: 2  
Explanation: The maximum width existing in the third level with the length 2 \(5,3\).

**Example 3:**

Input:

```text
      1
     / \
    3   2
   /        
  5      
```

Output: 2  
Explanation: The maximum width existing in the second level with the length 2 \(3,2\).

**Example 4:**

Input:

```text
      1
     / \
    3   2
   /     \  
  5       9
 /         \
6           7
```

Output: 8  
Explanation:The maximum width existing in the fourth level with the length 8 \(6,null,null,null,null,null,null,7\).

**Note:** Answer will in the range of 32-bit signed integer.

## Basic Idea:

求给定二叉树的最大宽度，最大宽度的两边必须在同一层中，最大宽度的定义为当前层如果填满leftMost和rightMost之间的Node，当前层node的个数。

可以利用数组存heap的思路，将node和index对应起来。利用queue进行层序遍历，每次和node一同enque的还有该node的index （left child 为 `index * 2`，right 为 `index * 2 + 1`）。每层考虑最左和最右index的差值+1，返回值就是这些值之中最大的。

### Java Code:

```java
class Solution {
    private class Node {
        TreeNode node;
        int index;
        public Node(TreeNode node, int index) {
            this.node = node;
            this.index = index;
        }
    }

    public int widthOfBinaryTree(TreeNode root) {
        if (root == null) return 0;
        else if (root.left == null && root.right == null) return 1;
        Deque<Node> queue = new ArrayDeque<>();
        queue.offerFirst(new Node(root, 0));
        int maxWidth = 0;
        while (! queue.isEmpty()) {
            int size = queue.size();
            int leftMost = Integer.MAX_VALUE, rightMost = Integer.MIN_VALUE;
            for (int i = 0; i < size; ++i) {
                Node node = queue.pollLast();
                leftMost = Math.min(leftMost, node.index);
                rightMost = Math.max(rightMost, node.index);
                if (node.node.left != null) {
                    queue.offerFirst(new Node(node.node.left, node.index * 2 + 1));
                }
                if (node.node.right != null) {
                    queue.offerFirst(new Node(node.node.right, node.index * 2 + 2));
                }
            }
            maxWidth = Math.max(maxWidth, rightMost - leftMost + 1);
        }
        return maxWidth;
    }
}
```

