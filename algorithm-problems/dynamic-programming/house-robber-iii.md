# House Robber III

_udpate Aug 6,2017 22:00_

[LeetCode](https://leetcode.com/problems/house-robber-iii/discuss/)

The thief has found himself a new place for his thievery again. There is only one entrance to this area, called the "root." Besides the root, each house has one and only one parent house. After a tour, the smart thief realized that "all houses in this place forms a binary tree". It will automatically contact the police if two directly-linked houses were broken into on the same night.

Determine the maximum amount of money the thief can rob tonight without alerting the police.

**Example 1:**

```text
     3
    / \
   2   3
    \   \ 
     3   1
Maximum amount of money the thief can rob = 3 + 3 + 1 = 7.
Example 2:
     3
    / \
   4   5
  / \   \ 
 1   3   1
```

Maximum amount of money the thief can rob = 4 + 5 = 9.

## Basic Idea:

**思路1：** 最初的思路就是直接使用dfs，brute force地求解。传入一个boolean参数标志当前node的parent是否被robbed，如果是true，则当前node不可以被抢，如果false，则当前node分为抢和不抢两种情况讨论，返回max。返回值构造答案的过程为自下而上，当root为null时返回0。

Java code:

```java
    public class Solution {
        public int rob(TreeNode root) {
            if (root == null) return 0;
            // 表示root的parent未被抢
            int ret = dfs(root, false);
            return ret;
        }
        // robbed表示其parent是否被抢，如果false，则两个方向，抢或者不抢，选多的返回
        // 如果true，则两个不抢，选多的返回
        private int dfs(TreeNode root, boolean robbed) {
            if (root == null) return 0;
            if (robbed) {
                // 如果父母被抢，则不抢这个，直接返回两个孩子的和
                int left = dfs(root.left, false);
                int right = dfs(root.right, false);
                return left + right;
            } else {
                // 如果父母没有被抢，则分情况考虑左右
                int left_case1 = dfs(root.left, false);
                int left_case2 = dfs(root.left, true);
                int right_case1 = dfs(root.right, false);
                int right_case2 = dfs(root.right, true);
                return Math.max(left_case1 + right_case1, left_case2 + right_case2 + root.val);
            }
        }
    }
```

**思路2：** 这个思路是前一种方法的优化。我们注意到在dfs的过程中，例如：

```text
     3
    / \
   4   5
  / \   \ 
 1   3   1
```

当我们在 （抢3，不抢4，抢1），和 （不抢3，不抢4，抢1）两种情况下，都要独立计算 node1 及其子树的情况，因此如果我们可以记录状态 （node（1），parent 未被抢），当我们下次遇到这种情况时候就可以直接返回，由此节省大量操作。

具体地，我们用一个`HashMap<TreeNode, Integer[]>`来存储每个状态的值，其中数组的`[0]表示其parent被抢的状态，[1]为未被抢`。

Java Code:

```java
    // with memoization
    public class Solution {
        public int rob(TreeNode root) {
            if (root == null) return 0;
            // 表示root的parent未被抢
            int ret = dfs(root, new HashMap<TreeNode, Integer[]>(), false);
            return ret;
        }
        // robbed表示其parent是否被抢，如果false，则两个方向，抢或者不抢，选多的返回
        // 如果true，则两个不抢，选多的返回
        // table [0] 存储 robbed， [1] 存parent未抢的情况
        private int dfs(TreeNode root, Map<TreeNode, Integer[]> table, boolean robbed) {
            if (root == null) return 0;
            if (! table.containsKey(root)) table.put(root, new Integer[2]);
            if (robbed) {
                if (table.get(root)[0] != null) return table.get(root)[0];
                // 如果父母被抢，则不抢这个，直接返回两个孩子的和
                int left = dfs(root.left, table, false);
                int right = dfs(root.right, table, false);
                table.get(root)[0] = left + right;
                return table.get(root)[0];
            } else {
                // 如果父母没有被抢，则分情况考虑左右
                if (table.get(root)[1] != null) return table.get(root)[1];
                int left_case1 = dfs(root.left, table, false);
                int left_case2 = dfs(root.left, table, true);
                int right_case1 = dfs(root.right, table, false);
                int right_case2 = dfs(root.right, table, true);
                int ret = Math.max(left_case1 + right_case1, left_case2 + right_case2 + root.val);
                table.get(root)[1] = ret;
                return ret;
            }
        }
    }
```

