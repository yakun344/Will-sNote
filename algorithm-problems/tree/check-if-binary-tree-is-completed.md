# Check If Binary Tree Is Completed

_update Mar 4, 2018 1:18_

Check if a given binary tree is completed. A complete binary tree is one in which every level of the binary tree is completely filled except possibly the last level. Furthermore, all nodes are as far left as possible.

**Examples**

```text
            5

          /    \

        3        8

      /   \

    1      4

    is completed.

            5

          /    \

        3        8

      /   \        \

    1      4        11

    is not completed.
```

**Corner Cases**

1. What if the binary tree is null?  

   > Return true in this case.

## Basic Idea:

Completed tree 就是除了最后一层，每层都满，并且最后一层的 nodes 都尽可能向左。要判断一个tree是否是completed，使用 BFS 逐层从左向右扫描。当node从左向右逐层扫描断开之后，就不可以再继续出现新的node（即相邻node间不可以有hole），满足这个条件就可以保证input tree是completed的。所以维持一个 `boolean finished` 变量来表示扫描是否已经断开。另外要注意，如果一个node的左孩子为空，而右孩子不为空，则可以直接返回false；

* **Java Code:**

  ```java
    public class Solution {
      public boolean isCompleted(TreeNode root) {
            if (root == null) return true;
        Deque<TreeNode> queue = new ArrayDeque<>();
        queue.offerFirst(root);
        boolean finished = false;
        while (! queue.isEmpty()) {
          TreeNode curr = queue.pollLast();
          if (finished && (curr.left != null || curr.right != null)) {
            // 断开后又有node，返回false
            return false;
          } else if (curr.left != null && curr.right == null) {
            // 左不空右空，finished
            finished = true;
          } else if (curr.left == null && curr.right != null) {
            // 左空右不空，返回false
            return false;
          }
          if (curr.left != null) queue.offerFirst(curr.left);
          if (curr.right != null) queue.offerFirst(curr.right);
        }
        return true;
      }
    }
  ```

_update 2018-06-17 16:59:05_

#### Update，之前解法有误

出现左孩子不为空，右孩子为空，或者两个孩子都为空，就应该将 finished 设为 true。之前的解法漏掉了两个 child 都为 null 的情况。

