# Flip Binary Tree To Match Preorder Traversal
_update Jan 8 2019, 23:19_

---
[LeetCode](https://leetcode.com/problems/flip-binary-tree-to-match-preorder-traversal/)

Given a binary tree with N nodes, each node has a different value from {1, ..., N}.

A node in this binary tree can be flipped by swapping the left child and the right child of that node.

Consider the sequence of N values reported by a preorder traversal starting from the root.  Call such a sequence of N values the voyage of the tree.

(Recall that a preorder traversal of a node means we report the current node's value, then preorder-traverse the left child, then preorder-traverse the right child.)

Our goal is to flip the least number of nodes in the tree so that the voyage of the tree matches the voyage we are given.

If we can do so, then return a list of the values of all nodes flipped.  You may return the answer in any order.

If we cannot do so, then return the list [-1].

 

**Example 1:**

![](https://assets.leetcode.com/uploads/2019/01/02/1219-01.png)

    Input: root = [1,2], voyage = [2,1]
    Output: [-1]

**Example 2:**

![](https://assets.leetcode.com/uploads/2019/01/02/1219-02.png)

    Input: root = [1,2,3], voyage = [1,3,2]
    Output: [1]
    
**Example 3:**

![](https://assets.leetcode.com/uploads/2019/01/02/1219-02.png)

    Input: root = [1,2,3], voyage = [1,2,3]
    Output: []
 
**Note:**

`1 <= N <= 100`

<br/>

### Basic Idea:
维持一个全局变量curr，标示下一个要遍历的 `voyage` 中的index。每次我们检查当前node的值和curr对应的是否相等，如果相等，我们就递归继续判断(先左后右)，如果不相等，我们就进行一次反转，相当于先判断right再判断left(如果反转之后还不相等，则返回false)。

之所以这样的方法是对的，是因为题目规定每个node的val是unique的，这样当我们发现`node.left.val == nextPreorderVal` 的时候，就可以放心进入下一层递归。

#### Java Code:
```java
class Solution {
    private int curr = 0;
    private List<Integer> res = new ArrayList<>();
    
    public List<Integer> flipMatchVoyage(TreeNode root, int[] voyage) {
        boolean ret = helper(root, voyage);
        if (ret && curr == voyage.length) return res;
        else {
            res.clear();
            res.add(-1);
            return res;
        }
    }
    
    private boolean helper(TreeNode node, int[] nums) {
        if (node == null) return true;
        if (curr >= nums.length || node.val != nums[curr]) return false;
        curr++;
        if (node.left != null && curr < nums.length && node.left.val != nums[curr]) {
            res.add(node.val);
            return helper(node.right, nums) && helper(node.left, nums);
        } else {
            return helper(node.left, nums) && helper(node.right, nums);
        }
    }
}
```
