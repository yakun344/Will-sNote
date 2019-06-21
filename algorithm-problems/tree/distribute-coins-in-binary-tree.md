# Distribute Coins in Binary Tree

_update Feb 13 2019, 1:01_

[LeetCode](https://leetcode.com/problems/distribute-coins-in-binary-tree/)

Given the root of a binary tree with N nodes, each node in the tree has node.val coins, and there are N coins total.

In one move, we may choose two adjacent nodes and move one coin from one node to another. \(The move may be from parent to child, or from child to parent.\)

Return the number of moves required to make every node have exactly one coin.

**Example 1:**

![](https://assets.leetcode.com/uploads/2019/01/18/tree1.png)

Input: \[3,0,0\] Output: 2 Explanation: From the root of the tree, we move one coin to its left child, and one coin to its right child.

**Example 2:**

![](https://assets.leetcode.com/uploads/2019/01/18/tree2.png)

Input: \[0,3,0\] Output: 3 Explanation: From the left child of the root, we move two coins to the root \[taking two moves\]. Then, we move one coin from the root of the tree to the right child.

**Example 3:**

![](https://assets.leetcode.com/uploads/2019/01/18/tree3.png)

Input: \[1,0,2\] Output: 2

**Example 4:**

![](https://assets.leetcode.com/uploads/2019/01/18/tree4.png)

Input: \[1,0,0,null,3\] Output: 4

**Note:**

1. `1<= N <= 100`
2. `0 <= node.val <= N`

## Basic Idea:

因为每次只能给相邻节点一个硬币，所以如果一个硬币被传递了很远，就需要被多次计算。那么我们其实可以用分治的思想讨论，对于一个node，我们考虑他一共需要给出或者从parent拿到多少硬币，把所有的这些值加起来就是结果了。例如example4中的例子，左下角node要传2个出来，然后左边中间那个传1个出来，然后root传一个到右边，刚好就是几个的值相加。

### Java Code:

```java
class Solution {
    private int ret = 0;

    public int distributeCoins(TreeNode root) {
        helper(root);
        return ret;
    }

    // 正数：多出来的coin
    // 负数：需要的coin
    // 0：刚好
    private int helper(TreeNode root) {
        if (root == null) return 0;
        int left = helper(root.left);
        int right = helper(root.right);
        ret += Math.abs(left + right + root.val - 1);
        return left + right + root.val - 1;
    }
}
```

