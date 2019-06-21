# Kth Smallest Element in a BST

_update Set 28 2018, 0:24_

[LeetCode](https://leetcode.com/problems/kth-smallest-element-in-a-bst/description/)

Given a binary search tree, write a function kthSmallest to find the kth smallest element in it.

**Note:**   
You may assume k is always valid, 1 ≤ k ≤ BST's total elements.

**Example 1:**

```text
Input: root = [3,1,4,null,2], k = 1
   3
  / \
 1   4
  \
   2
Output: 1
```

**Example 2:**

```text
Input: root = [5,3,6,2,4,null,null,1], k = 3
       5
      / \
     3   6
    / \
   2   4
  /
 1
Output: 3
```

**Follow up:**  
What if the BST is modified \(insert/delete operations\) often and you need to find the kth smallest frequently? How would you optimize the kthSmallest routine?

## Basic Idea:

基本思路可以inoder遍历，然后输出第k大的数。

对于follow up，可以使用augmented data structure的思路，在每个node中令外存储一个left subtree 的 size。如果无法改动node的结构，可以建一个hashMap来存储pair：`[node, leftSize]`，然后在增删改查树的时候进行相应的更新。

### Java Code：

naive解法：

```java
class Solution {
    public int kthSmallest(TreeNode root, int k) {
        Deque<TreeNode> stack = new ArrayDeque<>();
        TreeNode curr = root;
        while (curr != null) {
            stack.offerLast(curr);
            curr = curr.left;
        }
        int num = 0;
        while (! stack.isEmpty()) {
            curr = stack.pollLast();
            if (++num == k) return curr.val;
            curr = curr.right;
            while (curr != null) {
                stack.offerLast(curr);
                curr = curr.left;
            }
        }
        return -1;
    }
}
```

