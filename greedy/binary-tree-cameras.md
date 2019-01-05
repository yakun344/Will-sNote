# Binary Tree Cameras
_update Jan 3 19:52_

---
[LeetCode](https://leetcode.com/problems/binary-tree-cameras/)

Given a binary tree, we install cameras on the nodes of the tree. 

Each camera at a node can monitor its parent, itself, and its immediate children.

Calculate the minimum number of cameras needed to monitor all nodes of the tree.

 

**Example 1:**

![](https://assets.leetcode.com/uploads/2018/12/29/bst_cameras_01.png)

    Input: [0,0,null,0,0]
    Output: 1
    Explanation: One camera is enough to monitor all nodes if placed as shown.


**Example 2:**

![](https://assets.leetcode.com/uploads/2018/12/29/bst_cameras_02.png)

    Input: [0,0,null,0,null,0,null,null,0]
    Output: 2
    Explanation: At least two cameras are needed to monitor all nodes of the tree. 
        The above image shows one of the valid configurations of camera placement.

**Note:**

1. The number of nodes in the given tree will be in the range `[1, 1000]`.
2. Every node has value 0.

<br/>

### Basic Idea:
这里记录的解法来自lee215录制的一次直播参加leetcode contest的视频，到了这道题的时候lee一拍脑门给出了这个greedy的解法，感觉非常厉害。

基本思路就是从下往上考虑，如果我们拿到一个node，它的孩子都没有被cover，那么它必须有camera，如果它的孩子都被cover且没有相机，则让该node的parent来cover它，如果它的孩子有camera，则它本身被孩子cover。一共就只有这三种情况，我们只要从下往上依次判断，就可以解决问题。但是细节上有些地方需要注意，例如用不同返回数字表示不同的状态，用boolean来表示state之类的。以后需要注意这些细节。

#### Java Code:
```java
class Solution {
    private int count = 0;
    
    public int minCameraCover(TreeNode root) {
        int ret = helper(root);
        if (ret == 2) return count + 1;
        else return count;
    }
    
    // return
    // 0: covered by child
    // 1: has camera, covered
    // 2: need to be covered
    private int helper(TreeNode root) {
        boolean covered = false;
        boolean hasCamera = false;
        if (root.left == null && root.right == null) {
            return 2;
        }
        if (root.left != null) {
            int left = helper(root.left);
            if (left == 2) {
                hasCamera = true;
                covered = true;
            } else if (left == 1) {
                covered = true;
            }
        }
        if (root.right != null) {
            int right = helper(root.right);
            if (right == 2) {
                hasCamera = true;
                covered = true;
            } else if (right == 1) {
                covered = true;
            }
        }
        if (hasCamera) {
            count++;
            return 1;
        } 
        if (covered) return 0;
        else return 2;
    }
}
```
