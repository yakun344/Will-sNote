## Find Duplicate Subtrees
_update Aug 31, 2017  18:17_

---
[LeetCode](https://leetcode.com/problems/find-duplicate-subtrees/description/)

Given a binary tree, return all duplicate subtrees. For each kind of duplicate subtrees, you only need to return the root node of any one of them.

Two trees are duplicate if they have the same structure with same node values.

**Example 1: **

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
    
#### Basic Idea:
用dfs对tree进行 serialization，沿途把完结的子树的序列化字符串存入hashmap，key 是字符串，value 是 treeNode list；最终只要返回长度大于 1 的list中的某个node即可（都是相等子树的root）；

#### Python Code：
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




