## Path Sum III

_update Jun 27, 2017_

---

[leetcode](https://leetcode.com/problems/path-sum-iii/#/description)

You are given a binary tree in which each node contains an integer value.

Find the number of paths that sum to a given value.

The path does not need to start or end at the root or a leaf, but it must go downwards \(traveling only from parent nodes to child nodes\).

The tree has no more than 1,000 nodes and the values are in the range -1,000,000 to 1,000,000.

**Example**:

root = \[10,5,-3,3,2,null,11,3,-2,null,1\], sum = 8

```
        10
       /  \
      5   -3
     / \    \
    3   2   11
   / \   \
  3  -2   1
```

Return 3. The paths that sum to 8 are:

1. 5 -&gt; 3
2. 5 -&gt; 2 -&gt; 1
3. -3 -&gt; 11

**解释**下：这道题的意思就是说找任意path上相邻节点的和是 given sum value 的数量。  
**思路**： 这道题目的难点是要考虑从上到下每一段path上nodes的和。我们可以写两个 recursive function， 外层的用来对每个node（做为root）考虑其子树中的所有path。而内层 function 则用来考虑包含该 node（做为root）的所有path。  
**细节**： 对于内层function，每次调用下一层时候，传入下层的 sum 变为 sum - node.val，这样当下层 val == sum 的时候，就说明有了一条满足条件的path。

Python Code：

```python
    # python 2.7
    class Solution(object):
        def pathSum(self, root, sum):
            """
            :type root: TreeNode
            :type sum: int
            :rtype: int
            """
            if not root:
                return 0
            return self.dfs(root, sum) +
                   self.pathSum(root.left, sum) +
                   self.pathSum(root.right, sum)

        def dfs(self, node, target):
            if not node:
                return 0
            return (node.val == target) +
                   self.dfs(node.left, target - node.val) +
                   self.dfs(node.right, target - node.val)
```

Java code:

```java
    // java
    class Solution {
        public int pathSum(TreeNode root, int sum) {
            if (root == null) return 0;
            return helper(root, sum) + pathSum(root.left, sum) + pathSum(root.right, sum);
        }
        // 返回以当前node开始向下的符合条件的path数量
        private int helper(TreeNode root, int target) {
            if (root == null) return 0;
            if (root.val == target) return 1 + helper(root.left, 0) + helper(root.right, 0);
            else return helper(root.left, target - root.val) + helper(root.right, target - root.val);
        }
    }
```

<br>

---
_update Jan 27,2018 19:58_

## Update PreSum 数组 解法
还是利用 dfs，逐层向下构建当前path，不同的是path中存储的是之前每个node.val组成的数组的preSum数组，如此一来每层我们都可以用 `O(path.size)` 的时间查看目前为止有多少以该节点为终点的路径其 sum 为给定值，优化了时间复杂度。

**时间复杂度:** 时间应该为 `O(NlogN)` 因为每次需要 `O(当前depth)` 的时间来遍历presum数组，而depth最高为 `logN` ，一共有 N 个节点，所以上界应该为 `O(NlogN)`; 空间复杂度为 `O(height)`;

* #### Java Code:
```java
    class Solution {
        public int pathSum(TreeNode root, int sum) {
            List<Integer> preSum = new ArrayList<>();
            preSum.add(0);
            return helper(root, preSum, sum);
        }

        private int helper(TreeNode root, List<Integer> preSum, int sum) {
            if (root == null) return 0;
            int ret = 0;
            preSum.add(preSum.get(preSum.size() - 1) + root.val);
            for (int i = 0; i < preSum.size() - 1; ++i) {
                if (preSum.get(preSum.size() - 1) - preSum.get(i) == sum) {
                    ret++;
                }
            }
            int left = helper(root.left, preSum, sum);
            int right = helper(root.right, preSum, sum);
            preSum.remove(preSum.size() - 1);
            return ret + left + right;
        }
    }
```

* #### C++ Code:
```cpp
    class Solution {
        int helper(TreeNode* root, vector<int>& path, int sum) {
            if (root == nullptr) return 0;
            path.push_back(path.back() + root->val);
            int ret = 0;
            for (int i = 0; i < path.size() - 1; ++i) { // 注意这里，避免相同节点相减出现sum==0的假象
                if (path.back() - path[i] == sum) {
                    ret++;
                }
            }
            int left = helper(root->left, path, sum);
            int right = helper(root->right, path, sum);
            path.pop_back();
            return left + right + ret;
        }
    public:
        int pathSum(TreeNode* root, int sum) {
            vector<int> path{0};
            return helper(root, path, sum);
        }
    };
```

---
_update 2018-06-29 20:31:55_

#### Update: 使用 prefix sum + HashMap，最快解法
最初的解法是维持一个path，每到一个新的node，就向上遍历path，看有无 `sum==target` 的情况，这种做法的时间复杂度为 `O(N*(logN^2))`。

进一步优化，把path改成 prefix sum array，这样时间复杂度就变为了 `O(NlogN)`。

接下来，我们可以继续优化，使用 HashMap 来存储 prefix sums，即在 map 中存当前path中的 prefix sums 以及其个数，每次到了新node，向上query的时间变为 `O(1)`，总时间复杂度降为 `O(N)`;

```java
class Solution {
    private int ret = 0;
    public int pathSum(TreeNode root, int sum) {
        Map<Integer, Integer> path = new HashMap<>();
        path.put(0, 1);
        dfs(root, path, 0, sum);
        return ret;
    }
    private void dfs(TreeNode root, Map<Integer, Integer> path, int lastSum, int sum) {
        if (root == null) return;
        int currSum = lastSum + root.val;
        ret += path.getOrDefault(currSum - sum, 0);
        path.put(currSum, path.getOrDefault(currSum, 0) + 1);
        dfs(root.left, path, currSum, sum);
        dfs(root.right, path, currSum, sum);
        path.put(currSum, path.get(currSum) - 1);
    }
}
```
