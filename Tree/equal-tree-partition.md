## Equal Tree Partition
_update Aug 29, 2017  19:28_

---

[LeetCode](https://leetcode.com/problems/equal-tree-partition/description/)

Given a binary tree with n nodes, your task is to check if it's possible to partition the tree to two trees which have the equal sum of values after removing exactly one edge on the original tree.

**Example 1:**
        Input:     
            5
           / \
          10 10
            /  \
           2   3
        
        Output: True
        Explanation: 
            5
           / 
          10
              
        Sum: 15
        
           10
          /  \
         2    3
        
        Sum: 15
**Example 2:**

        Input:     
            1
           / \
          2  10
            /  \
           2   20

**Output**: 

        False

**Explanation:** 

You can't split the tree into two trees with equal sum after removing exactly one edge on the tree.

**Note:**

1. The range of tree node value is in the range of [-100000, 100000].
2. `1 <= n <= 10000`

#### Basic Idea:

通过观察我们注意到，只要有子树的sum等于sum\_total的一半，我们就可以切断该子树与其他部分相连的边而达到目的。但是，还要考虑两种特殊情况：

* total\_sum 是负数：这种情况其实也并非特殊，无需特别处理；
* total\_sum 是 0： 这种情况需要特别注意。当总sum为0时，只有存在sum为0的子树才可以，这意味着我们不可以因为其他原因把0加入sums set，例如接触到null结点；

#### Java Code:

```java
    // 记录每个子树的sum，如果有子树sum使得 sum * 2 == total_sum, 则 return True
    class Solution {
        private Set<Integer> sums;
        public boolean checkEqualTree(TreeNode root) {
            if (root == null) return false;
            sums = new HashSet<>();
            int sum_total = getSum(root);
            if (sum_total % 2 == 0 && sums.contains(sum_total / 2)) {
                return true;
            }
            return false;
        }
        // 返回当前root为根的子树的sum，存入HashSet sums
        private int getSum(TreeNode root) {
            if (root.left == null && root.right == null) return root.val;
            int left_sum = 0, right_sum = 0;
            if (root.left != null) {
                left_sum = getSum(root.left);
                sums.add(left_sum);
            }
            if (root.right != null) {
                right_sum = getSum(root.right);
                sums.add(right_sum);
            }
            int sum = left_sum + right_sum + root.val;
            return sum;
        }
    }
```



