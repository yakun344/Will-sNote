## Smallest String Starting From Leaf
_update Feb 3 2019, 23:41_

---
[LeetCode](https://leetcode.com/contest/weekly-contest-122/problems/smallest-string-starting-from-leaf/)

Given the root of a binary tree, each node has a value from 0 to 25 representing the letters 'a' to 'z': a value of 0 represents 'a', a value of 1 represents 'b', and so on.

Find the lexicographically smallest string that starts at a leaf of this tree and ends at the root.

(As a reminder, any shorter prefix of a string is lexicographically smaller: for example, "ab" is lexicographically smaller than "aba".  A leaf of a node is a node that has no children.)

 

**Example 1:**

![](https://assets.leetcode.com/uploads/2019/01/30/tree1.png)

Input: [0,1,2,3,4,3,4]
Output: "dba"

**Example 2:**

![](https://assets.leetcode.com/uploads/2019/01/30/tree2.png)

Input: [25,1,3,1,3,0,2]
Output: "adz"

**Example 3:**

![](https://assets.leetcode.com/uploads/2019/02/01/tree3.png)

Input: [2,2,1,null,1,0,null,0]
Output: "abc"
 

**Note:**

1. The number of nodes in the given tree will be between 1 and 1000.
2. Each node in the tree will have a value between 0 and 25.

<br/>

### Basic Idea:
这道题是求所有从leaf出发到root结尾道path中，沿途经过node的val组成的string中字典顺序最小的string。

我们可以用分治的思路去解决，对于当前node，从leaf出发到该node结尾的path中最小的，一定是到它左孩子和到它右孩子结尾路径中最小的再append上node.val。这就是基本的思路，具体实现中还需要注意如果只有一边有子节点，则直接返回到该子节点的最小string再append上node本身。

#### Java Code:
```java
    class Solution {
        public String smallestFromLeaf(TreeNode root) {
            if (root == null) return null;
            String currNode = (char)(root.val + 'a') + "";
            String leftMin = smallestFromLeaf(root.left);
            String rightMin = smallestFromLeaf(root.right);
            if (leftMin == null && rightMin == null) {
                return currNode;  
            } else if (leftMin == null || rightMin == null) {
                return (leftMin == null ? rightMin : leftMin) + currNode;
            } else {
                return (leftMin.compareTo(rightMin) < 0 ? leftMin : rightMin) + currNode;
            }
        }
    }
```
