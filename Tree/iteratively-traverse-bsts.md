## Iteratively Traverse a BST
_update Jun 21, 2017_

---
**LeetCode problems:**  
[Inorder](https://leetcode.com/problems/binary-tree-inorder-traversal/description/)  
[Preorder](https://leetcode.com/problems/binary-tree-preorder-traversal/description/)  
[Postorder](https://leetcode.com/problems/binary-tree-postorder-traversal/description/)  
<br>

这里所讲的`BST Traversal`方法指的主要是`preorder, inorder, postorder`，严格意义上三种都属于`DFS`。用`recursion`做BST的`Traversal`比较简单，思路很直接，但是如果要用`Iterative`的方法，就要用到`stack`，没有那么直观。这里主要提供三种遍历的`java`实现。

### Preorder
这个显然最简单，先root，再左右，直接上代码。

```java
    // java
    private void helper(TreeNode root) {
        Deque<TreeNode> stack = new LinkedList<>();
        if (root != null) stack.add(root);
        while (stack.size() > 0) {
            TreeNode curr = stack.removeLast();
            System.out.print(" " + curr.val);
            if (curr.right != null) stack.add(curr.right);
            if (curr.left != null) stack.add(curr.left);
        }
    }
```

### Inorder
Inorder 稍微复杂一些，可以画示意图理解。

基本算法是先一路向左，push in stack。然后keep popping，visit each node之后检查其是否有 right child， 如果有，把 right child 当做 root，一路向左，push in stack。
    
```java
    // java
    private void helper(TreeNode root) {
        Deque<TreeNode> stack = new LinkedList<>();
        while (root != null) {
        stack.add(root);
        root = root.left;
        } // 先一路向左push in stack
        
        while (stack.size() > 0) {
            TreeNode node = stack.removeLast();
            System.out.print(" " + node.val);
            // 如果有right child，一路向左 push
            if (node.right != null) {
                node = node.right;
                while (node != null) {
                    stack.add(node);
                    node = node.left;
                }
            }
        }
    }
```

### Postorder
这道题目在LeetCode里居然是hard。。这或许是我见过的最草率的hard题目了，233333；[LeetCode](https://leetcode.com/problems/binary-tree-postorder-traversal/description/)

Postorder 是最复杂的，有两种实现方法，一种用两个stack，另一种只用一个。这里提供只用一个stack的。

基本思想是先像 inorder 一样把node一路向左 push in stack。然后每次检查`stack.peek`，如果`peek` 没有`right child` 或者 `rc == curr`即右孩子刚刚 visited 过，则另`curr = stack.pop()`，visit 这个 node。如果`peek`有`right child`且没有 visited 过，则把它当做root，一路向左 push in stack。
    
```java
    class Solution {
        public List<Integer> postorderTraversal(TreeNode root) {
            List<Integer> res = new ArrayList<>();
            if (root == null) return res;
            Deque<TreeNode> stack = new LinkedList<>();
            TreeNode curr = root;
            while (curr != null) {
                stack.addLast(curr);
                curr = curr.left;
            }
            TreeNode prev = null;
            while (! stack.isEmpty()) {
                // 说明当前栈顶元素没有右子树或者之前已经处理过右子树，接下来就需要处理栈顶元素
                if (stack.peekLast().right == null || stack.peekLast().right == prev) {
                    curr = stack.removeLast();
                    res.add(curr.val);
                    prev = curr;
                } else {
                    // 此时需要先处理peek的right子树
                    curr = stack.peekLast().right;
                    while (curr != null) {
                        stack.addLast(curr);
                        curr = curr.left;
                    }
                }
            }
            return res;
        }
    }
```
*注意： 这个方法是可以求该 BST 的 max hight 的，就是 stack 的最大 size*

