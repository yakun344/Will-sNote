# Maximum Binary Tree II
_update Mar 5 2019, 18:56_

---
[LeetCode](https://leetcode.com/problems/maximum-binary-tree-ii/)

We are given the root node of a maximum tree: a tree where every node has a value greater than any other value in its subtree.

Just as in the previous problem, the given tree was constructed from an list A `(root = Construct(A))` recursively with the following Construct(A) routine:

If A is empty, return null.
Otherwise, let `A[i]` be the largest element of A.  Create a root node with value A`[i]`.
The left child of root will be `Construct([A[0], A[1], ..., A[i-1]])`
The right child of root will be `Construct([A[i+1], A[i+2], ..., A[A.length - 1]])`
Return root.
Note that we were not given A directly, only a root node `root = Construct(A)`.

Suppose B is a copy of A with the value val appended to it.  It is guaranteed that B has unique values.

Return `Construct(B)`.


**Example 1:**

![](https://assets.leetcode.com/uploads/2019/02/21/maximum-binary-tree-1-1.png)
![](https://assets.leetcode.com/uploads/2019/02/21/maximum-binary-tree-1-2.png)

Input: root = [4,1,3,null,null,2], val = 5
Output: [5,4,null,1,3,null,null,2]
Explanation: A = [1,4,2,3], B = [1,4,2,3,5]

**Example 2:**

![](https://assets.leetcode.com/uploads/2019/02/21/maximum-binary-tree-2-1.png)
![](https://assets.leetcode.com/uploads/2019/02/21/maximum-binary-tree-2-2.png)

Input: root = [5,2,4,null,1], val = 3
Output: [5,2,4,null,1,null,3]
Explanation: A = [2,1,5,4], B = [2,1,5,4,3]

**Example 3:**

![](https://assets.leetcode.com/uploads/2019/02/21/maximum-binary-tree-3-1.png)
![](https://assets.leetcode.com/uploads/2019/02/21/maximum-binary-tree-3-2.png)

Input: root = [5,2,3,null,1], val = 4
Output: [5,2,4,null,1,3]
Explanation: A = [2,1,5,3], B = [2,1,5,3,4]
 

**Note:**

`1 <= B.length <= 100`

<br/>

## Basic Idea:
题意是说有一种 max tree，输入一个数组，这个tree的root是最大的，然后每次选出最大的作为子树的root。root左边部分就是数组中root.val的左边元素们，root右边就是数组中root.val右边的元素们。然后说给定一个这样的数，再给一个要插在数组最右侧的val，返回最终的tree root。

我们可以从root开始递归求解：

1. 如果当前root为null，则用val新建node返回；
2. 如果当前root的值比val小，则val需要成为新的root，并且当前root成为新root的左孩子；
3. 如果当前root的值大于val，则val一定在root的右侧，于是递归求解root的右侧与val；

### Java Code:
```java
    class Solution {
        public TreeNode insertIntoMaxTree(TreeNode root, int val) {
            if (root == null) return new TreeNode(val);
            if (root.val < val) {
                TreeNode ret = new TreeNode(val);
                ret.left = root;
                return ret;
            }
            root.right = insertIntoMaxTree(root.right, val);
            return root;
        }
    }
```
