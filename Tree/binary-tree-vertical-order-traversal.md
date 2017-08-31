## Binary Tree Vertical Order Traversal
_update Aug 31, 2017  15:33_

---
[LeetCode](https://leetcode.com/problems/binary-tree-vertical-order-traversal/description/)

Given a binary tree, return the vertical order traversal of its nodes' values. (ie, **from top to bottom, column by column**).

If two nodes are in the same row and column, the order should be from left to right.

**Examples:**

      Given binary tree [3,9,20,null,null,15,7],
         3
        /\
       /  \
       9  20
          /\
         /  \
        15   7
      return its vertical order traversal as:
      [
        [9],
        [3,15],
        [20],
        [7]
      ]
      Given binary tree [3,9,8,4,0,1,7],
           3
          /\
         /  \
         9   8
        /\  /\
       /  \/  \
       4  01   7
      return its vertical order traversal as:
      [
        [4],
        [9],
        [3,0,1],
        [8],
        [7]
      ]
      Given binary tree [3,9,8,4,0,1,7,null,null,null,2,5] 
      (0's right child is 2 and 1's left child is 5),
           3
          /\
         /  \
         9   8
        /\  /\
       /  \/  \
       4  01   7
          /\
         /  \
         5   2
      return its vertical order traversal as:
      [
        [4],
        [9,5],
        [3,0,1],
        [8,2],
        [7]
      ]
      
#### Basic Idea:
根据题目所给条件，我们需要跟踪每个node在垂直方向上的index，但是我们一开始只能接触到接近中心col位置的root，要向左右两边扩展，我首先想到了用LinkedList，因为array向左扩展不方便。事实证明用链表加DFS也不好做，因为无法保证右子树较高的元素在同样col的list中先出现。

最终发现，用 HashMap + BFS 最为方便：
-  新建一个QElement class，其中除 TreeNode 外还有一个 int col 属性，用来标识节点在水平方向上的位置。root 的 col 为0，左边 -1，右边 +1；
-  HashMap 的key为 col，value 为该 col 上的 vals list；
-  每次出队元素之后，将其val加入对应的col的list中，再把左右两子树更新过col之后加入queue；
-  最终，按照从左到右的顺序遍历 HashMap，将所有的list放入res中返回；

#### Java Code:
```java
    class Solution {
        private class QElement {
            int col;
            TreeNode node;
            public QElement(int col, TreeNode node) {
                this.col = col;
                this.node = node;
            }
        }
        public List<List<Integer>> verticalOrder(TreeNode root) {
            Deque<QElement> queue = new LinkedList<>();
            int minCol = Integer.MAX_VALUE;
            int maxCol = Integer.MIN_VALUE;
            List<List<Integer>> res = new ArrayList<>();
            if (root == null) return res;
            // 以root所在的col为0，左边为负数，右边为正数，作为key，每个value为一个list，跟踪col的范围
            Map<Integer, List<Integer>> map = new HashMap<>();
            
            // BFS，每次将node.val放入相应col的list中
            queue.addFirst(new QElement(0, root));
            while (! queue.isEmpty()) {
                QElement qe = queue.removeLast();
                if (! map.containsKey(qe.col)) map.put(qe.col, new ArrayList<Integer>());
                map.get(qe.col).add(qe.node.val);
                minCol = Math.min(minCol, qe.col);
                maxCol = Math.max(maxCol, qe.col);
                if (qe.node.left != null) queue.addFirst(new QElement(qe.col - 1, qe.node.left));
                if (qe.node.right != null) queue.addFirst(new QElement(qe.col + 1, qe.node.right));
            }
            
            // 把所有col的list从左到右放入res；
            for (int i = minCol; i <= maxCol; ++i) {
                if (! map.containsKey(i)) continue;
                res.add(map.get(i));
            }
            return res;
        }
    }
```

