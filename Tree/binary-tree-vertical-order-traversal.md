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

另外，python中可以直接在queue中存tuple，所以不需要定义内部类。Java的方法，使用两个queue速度更快；

#### Java Code:
```java
    class Solution {
        private class Tuple {
            int col;
            TreeNode node;
            Tuple(int col, TreeNode node) {
                this.col = col;
                this.node = node;
            }
        }
        
        public List<List<Integer>> verticalOrder(TreeNode root) {
            List<List<Integer>> res = new ArrayList<>();
            if (root == null) return res;
            Map<Integer, List<Integer>> colMap = new HashMap<>();
            Deque<Tuple> queue = new LinkedList<>();
            int minCol = Integer.MAX_VALUE;
            
            queue.addFirst(new Tuple(0, root));
            while (! queue.isEmpty()) {
                Tuple tuple = queue.removeLast();
                int col = tuple.col;
                TreeNode node = tuple.node;
                minCol = Math.min(minCol, col);
                
                if (! colMap.containsKey(col)) colMap.put(col, new ArrayList<>());
                colMap.get(col).add(node.val);
                
                if (node.left != null) queue.addFirst(new Tuple(col - 1, node.left));
                if (node.right != null) queue.addFirst(new Tuple(col + 1, node.right));
            }
            
            for (int col = minCol; col < minCol + colMap.size(); ++col) {
                res.add(colMap.get(col));
            }
            return res;
        }
    }
```
<br>

---
_update Dec 24, 2017  14:43_

### Update
**Python Code:**  
queue中存两个量，不需要定义新的类：
```python
    class Solution:
        def verticalOrder(self, root):
            """
            :type root: TreeNode
            :rtype: List[List[int]]
            """
            ret = []
            if not root: return ret
            colMap = collections.defaultdict(list)
            queue = collections.deque()
            queue.appendleft((0, root))
            minCol = float('inf')
            while queue:
                col, node = queue.pop()
                minCol = min(minCol, col)
                colMap[col].append(node.val)
                if node.left:
                    queue.appendleft((col - 1, node.left))
                if node.right:
                    queue.appendleft((col + 1, node.right))
            for i in range(minCol, minCol + len(colMap)):
                ret.append(colMap[i])
            return ret
```















