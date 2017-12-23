## Two Sum IV - Input is a BST
_update Aug 18, 2017  22:52_

---
[LeetCode](https://leetcode.com/problems/two-sum-iv-input-is-a-bst/description/)


Given a Binary Search Tree and a target number, return true if there exist two elements in the BST such that their sum is equal to the given target.

**Example 1:**

        Input: 
            5
           / \
          3   6
         / \   \
        2   4   7
        
        Target = 9
        
        Output: True
**Example 2:**

        Input: 
            5
           / \
          3   6
         / \   \
        2   4   7
        
        Target = 28
        
        Output: False
        
#### Basic Idea:
有两种方法：
1.  遍历bst，利用hashSet看之前有无出现 `target - curr.val`；
2.  in order 遍历bst，结果存入数组，则此时数组已经排序，然后用 two pointers 的方法，i j 从最左端和最右端开始，如果和大于target，则 j--， 如果小于，则 i++；

#### Python Code：
**方法 1：**
```python
    class Solution:
        def findTarget(self, root, k):
            """
            :type root: TreeNode
            :type k: int
            :rtype: bool
            """
            def dfs(root, k):
                if root is None:
                    return False
                if k - root.val in st:
                    return True
                st.add(root.val)
                return dfs(root.left, k) or dfs(root.right, k)
                
            st = set()
            return dfs(root, k)
```
**方法 2：**
```python
    class Solution:
        def findTarget(self, root, k):
            """
            :type root: TreeNode
            :type k: int
            :rtype: bool
            """
            def inOrder(root):
                if root is None:
                    return
                inOrder(root.left)
                lst.append(root.val)
                inOrder(root.right)
                
            lst = []
            inOrder(root)
            # 用left和right逐渐逼近找sum == target的值
            left = 0
            right = len(lst) - 1
            while left < right:
                sm = lst[left] + lst[right]
                if sm == k:
                    return True
                elif sm > k:
                    right -= 1
                else:
                    left += 1
            return False
```
<br>

---
_udpate Dec23, 2017  0:57_

### Update 
#### Java Code:
```java
    class Solution {
        public boolean findTarget(TreeNode root, int k) {
            Set<Integer> st = new HashSet<>();
            return dfs(root, k, st);
        }
        
        private boolean dfs(TreeNode root, int k, Set<Integer> st) {
            if (root == null) return false;
            if (st.contains(k - root.val)) return true;
            st.add(root.val);
            return dfs(root.left, k, st) || dfs(root.right, k, st);
        }
    }
```