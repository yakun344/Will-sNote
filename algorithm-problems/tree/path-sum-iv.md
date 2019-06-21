# Path Sum IV

_update Aug 31, 2017 0:02_

[LeetCode](https://leetcode.com/problems/path-sum-iv/description/)

If the depth of a tree is smaller than`5`, then this tree can be represented by a list of three-digits integers.

For each integer in this list:

1. The hundreds digit represents the depth`D`of this node,`1 <= D <= 4`.
2. The tens digit represents the positionPof this node in the level it belongs to,`1<= P<= 8`. The position is the same as that in a full binary tree.
3. The units digit represents the valueVof this node,`0<= V<= 9`.

Given a list of`ascending`three-digits integers representing a binary with the depth smaller than 5. You need to return the sum of all paths from the root towards the leaves.

**Example 1:**

```text
    Input:
     [113, 215, 221]

    Output:
     12

    Explanation:

    The tree that the list represents is:
        3
       / \
      5   1

    The path sum is (3 + 5) + (3 + 1) = 12.
```

**Example 2:**

```text
    Input:
     [113, 221]

    Output:
     4

    Explanation:

    The tree that the list represents is: 
        3
         \
          1

    The path sum is (3 + 1) = 4.
```

### Basic Idea:

破题点就在于所给的信息其实相当于告诉了我们每个 node 在一个完全二叉树中的精确位置，一想到这种设定，我们马上自然想到了 heap，于是一种解法自然成型：利用heap保存二叉树的方法，通过所给数字中的位置信息直接计算出其在数组中的index。

具体实现的过程中，有两个思路： 1. 和heap的保存方法一样，使用一个数组，由于至多有四层，考虑到可能需要探测的孩子节点的index，我们可以使用一个 Integer\[32\]（null 表示不存在）； 2. 也可以使用一个 `HashMap<Integer, Integer>` 来存，key是index，这样就可用contains\(\) 来判断孩子是否存在；

### Java Code:

方法1，使用Integer\[32\]:

```java
    // 用数组表示二叉树，类似于heap的表示法，然后就和普通的求path sum 没有区别了
    class Solution {
        int pathSum;
        public int pathSum(int[] nums) {
            if (nums == null || nums.length == 0) return 0;
            Integer[] tree = new Integer[32];
            Arrays.fill(tree, null);

            // 构建树
            for (int num : nums) {
                int index = (int)Math.pow(2, num / 100 - 1) + (num / 10) % 10 - 1;
                tree[index] = num % 10;
            }

            // 计算path sum
            pathSum = 0;
            helper(tree, 1, 0);
            return pathSum;
        }

        private void helper(Integer[] tree, int root, int path) {
            if (tree[root] == null) return;
            if (tree[getLeft(root)] == null && tree[getRight(root)] == null) {
                pathSum += path + tree[root];
                return;
            }
            helper(tree, getLeft(root), path + tree[root]);
            helper(tree, getRight(root), path + tree[root]);
        }

        private int getLeft(int i) {
            return 2 * i;
        }
        private int getRight(int i) {
            return 2 * i + 1;
        }
    }
```

方法2， 使用 HashMap：

```java
    // 使用 hashmap 存放每个node，key 即为每个node用heap形式表示时候的index，这样和之前类似，可以直接检查
    // 一个node的左右孩子是否存在
    class Solution {
        int pathSum;
        public int pathSum(int[] nums) {
            if (nums == null || nums.length == 0) return 0;
            // 先将每个节点和其index存入map
            Map<Integer, Integer> tree = new HashMap<>();
            for (int num : nums) {
                tree.put(getIndex(num), num % 10);
            }
            // 然后从root（1）出发，求和
            helper(tree, 1, 0);
            return pathSum;
        }
        private void helper(Map<Integer, Integer> tree, int root, int path) {
            if (! tree.containsKey(root)) return;
            if (! tree.containsKey(getLeft(root)) && ! tree.containsKey(getRight(root))) {
                pathSum += path + tree.get(root);
            }
            helper(tree, getLeft(root), path + tree.get(root));
            helper(tree, getRight(root), path + tree.get(root));
        }

        private int getIndex(int input) {
            return (int)Math.pow(2, input / 100 - 1) + input / 10 % 10 - 1;
        }
        private int getLeft(int i) {
            return 2 * i;
        }
        private int getRight(int i) {
            return 2 * i + 1;
        }
    }
```

_update Dec 21, 2017 1:26_

## Update

更新一个 python code，用 dict 实现 tree，居然在 python3 的答案中是最快的。

**Python Code:**

```python
    class Solution:
        def pathSum(self, nums):
            """
            :type nums: List[int]
            :rtype: int
            """
            def helper(root, path):
                if not root in tree: return 
                path += tree[root]
                if not root * 2 in tree and not root * 2 + 1 in tree:
                    self.ret += path
                else:
                    helper(root * 2, path)
                    helper(root * 2 + 1, path)


            self.ret = 0
            tree = {}
            for node in nums:
                tree[2 ** (node // 100 - 1) + node % 100 // 10 - 1] = node % 10
            helper(1, 0)
            return self.ret
```

