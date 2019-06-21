# Vertical Order Traversal of a Binary Tree

_update Feb 10 2019, 17:06_

[LeetCode](https://leetcode.com/problems/vertical-order-traversal-of-a-binary-tree/)

Given a binary tree, return the vertical order traversal of its nodes values.

For each node at position \(X, Y\), its left and right children respectively will be at positions \(X-1, Y-1\) and \(X+1, Y-1\).

Running a vertical line from X = -infinity to X = +infinity, whenever the vertical line touches some nodes, we report the values of the nodes in order from top to bottom \(decreasing Y coordinates\).

If two nodes have the same position, then the value of the node that is reported first is the value that is smaller.

Return an list of non-empty reports in order of X coordinate. Every report will have a list of values of nodes.

**Example 1:**

![](https://assets.leetcode.com/uploads/2019/01/31/1236_example_1.PNG)

```text
Input: [3,9,20,null,null,15,7]
Output: [[9],[3,15],[20],[7]]
Explanation: 
Without loss of generality, we can assume the root node is at position (0, 0):
Then, the node with value 9 occurs at position (-1, -1);
The nodes with values 3 and 15 occur at positions (0, 0) and (0, -2);
The node with value 20 occurs at position (1, -1);
The node with value 7 occurs at position (2, -2).
```

**Example 2:**

![](https://assets.leetcode.com/uploads/2019/01/31/tree2.png)

```text
Input: [1,2,3,4,5,6,7]
Output: [[4],[2],[1,5,6],[3],[7]]
Explanation: 
The node with value 5 and the node with value 6 have the same position according to the given scheme.
However, in the report "[1,5,6]", the node value of 5 comes first since 5 is smaller than 6.
```

**Note:**

1. The tree will have between 1 and 1000 nodes.
2. Each node's value will be between 0 and 1000.

## Basic Idea:

与之前类似的一道题目不同，这道题目除了在相同col元素按照从上到下顺序之外另外要求了同样position的元素要按照值的大小从小到大排列。所以基本思路可以使用一个 `TreeMap<Integer, TreeSet<int[2]>>` 来存每个Node，其中Key是col， `int[2]`中存的是`node.val`以及node的depth。TreeMap可以按照col排序，TreeSet可以将每组相同col的node按照从上到下，从小到大的顺序排序。这样我们先用一个dfs populate这个treemap，然后按顺序生成res list即可。时间复杂度为 O\(NlogN\), 因为需要用到BST。

### Java Code:

```java
    class Solution {
        private TreeMap<Integer, TreeSet<int[]>> bst;
        // a[0]存val，a[1]存depth
        private Comparator<int[]> comp = (int[] a, int[] b)->{
            if (a[1] != b[1]) {
                return Integer.compare(a[1], b[1]);
            } else {
                return Integer.compare(a[0], b[0]);
            }
        };

        public List<List<Integer>> verticalTraversal(TreeNode root) {
            bst = new TreeMap<>();
            List<List<Integer>> res = new ArrayList<>();

            if (root == null) return res;

            dfs(root, 0, 0);

            for (Map.Entry<Integer, TreeSet<int[]>> entry : bst.entrySet()) {
                TreeSet<int[]> col = entry.getValue();
                List<Integer> currColList = new ArrayList<>();
                for (int[] valAndDepth : col) {
                    currColList.add(valAndDepth[0]);
                }
                res.add(currColList);
            }
            return res;
        }

        private void dfs(TreeNode root, int col, int depth) {
            if (root == null) return;
            if (!bst.containsKey(col)) bst.put(col, new TreeSet<>(comp));
            bst.get(col).add(new int[]{root.val, depth});
            dfs(root.left, col - 1, depth + 1);
            dfs(root.right, col + 1, depth + 1);
        }
    }
```

