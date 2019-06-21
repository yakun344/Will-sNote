# Find Duplicate Subtrees

_update Aug 31, 2017 18:17_

[LeetCode](https://leetcode.com/problems/find-duplicate-subtrees/description/)

Given a binary tree, return all duplicate subtrees. For each kind of duplicate subtrees, you only need to return the root node of any one of them.

Two trees are duplicate if they have the same structure with same node values.

**Example 1:**

```text
            1
           / \
          2   3
         /   / \
        4   2   4
           /
          4
The following are two duplicate subtrees:

          2
         /
        4

and

        4

 Therefore, you need to return above trees' root in the form of a list.
```

### Basic Idea:

用dfs对tree进行 serialization，沿途把完结的子树的序列化字符串存入hashmap，key 是字符串，value 是 treeNode list；最终只要返回长度大于 1 的list中的某个node即可（都是相等子树的root）；

### Python Code：

```python
    class Solution(object):
        def findDuplicateSubtrees(self, root):
            """
            :type root: TreeNode
            :rtype: List[TreeNode]
            """

            def dfs(root):
                if not root: return ''
                ans = ''
                if not root.left and not root.right:
                    ans = str(root.val)
                else:
                    ans = str(root.val) + "(" + dfs(root.left) + "," + dfs(root.right) + ")"
                table[ans].append(root)
                return ans

            table = collections.defaultdict(list)
            dfs(root)
            return [table[elem][0] for elem in table if len(table[elem]) > 1]
```

### Java Code:

```java
    class Solution {
        public List<TreeNode> findDuplicateSubtrees(TreeNode root) {
            List<TreeNode> res = new ArrayList<>();
            Map<String, List<TreeNode>> map = new HashMap<>();
            helper(root, map, res);
            for (Map.Entry<String, List<TreeNode>> entry : map.entrySet()) {
                List<TreeNode> nodeList = entry.getValue();
                if (nodeList.size() > 1) res.add(nodeList.get(0));
            }
            return res;
        }

        private String helper(TreeNode root, Map<String, List<TreeNode>> map, List<TreeNode> res) {
            if (root == null) return "#";
            String left = helper(root.left, map, res);
            String right = helper(root.right, map, res);
            String ret = root.val + "," + left + "," + right;
            if (! map.containsKey(ret)) map.put(ret, new ArrayList<>());
            map.get(ret).add(root);
            return ret;
        }
    }
```

_update Dec 24, 2017 17:36_

## Update

重写此题的时候，没有用之前的 hashMap 的方法，而是用了两个 Hashset：

1. 大体思路和之前的方法类似，都是采用沿途得到以每个node为root的subtree的序列化string，与之前出现过的string相比较；
2. 这次试用 `HashSet<String> set`存放之前出现过的String，当再次遇到同样String时，将其node加入res，同时将该 string 加入 `HashSet<String> resSet` ，再遇到的时候就可以检查其是否在 `resSet` 中，避免重复添加。

感觉这种方法和之前的互有利弊，之前的方法会存多个node在map中，浪费空间，而这个方法需要查找两次set，浪费时间。

**Java Code:**

```java
class Solution {
    public List<TreeNode> findDuplicateSubtrees(TreeNode root) {
        List<TreeNode> res = new ArrayList<>();
        helper(root, new HashSet<>(), new HashSet<>(), res);
        return res;
    }
    // return inorder serialization of root, add the string into hashSet
    private String helper(TreeNode root, Set<String> set, Set<String> resSet, List<TreeNode> res) {
        if (root == null) return "#";
        String left = helper(root.left, set, resSet, res);
        String right = helper(root.right, set, resSet, res);
        String ret = root.val + "," + left + "," + right;
        if (! set.add(ret) && resSet.add(ret)) res.add(root); // set.add() 返回 false 说明set中已经存在
        return ret;
    }
}
```

_update 2018-06-07 21:56:57_

## Update C++ Solution

利用两个 hashSet 的解法， C++ code:

```cpp
class Solution {
    // post-order traverse, serialize, store string in hashSet, if found a string already in set,
    // means we have found a dup tree
    string helper(TreeNode* root, unordered_set<string>& count_set, vector<TreeNode*>& res, unordered_set<string>& res_set) {
        if (root == nullptr) return "#";
        string left_str = helper(root->left, count_set, res, res_set);
        string right_str = helper(root->right, count_set, res, res_set);
        string curr_str = left_str + "," + right_str + "," + to_string(root->val);
        if (count_set.insert(curr_str).second == false) {
            if (res_set.insert(curr_str).second == true) res.push_back(root);
        }
        return curr_str;
    }
public:
    vector<TreeNode*> findDuplicateSubtrees(TreeNode* root) {
        unordered_set<string> count_set;
        unordered_set<string> res_set;
        vector<TreeNode*> res;
        if (root == nullptr) return res;
        helper(root, count_set, res, res_set);
        return res;
    }
};
```

