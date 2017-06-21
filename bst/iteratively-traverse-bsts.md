## Iteratively Traverse a BST
_update Jun 21, 2017_

---
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
    * 基本算法是先一路向左，push in stack。然后keep popping，visit each node之后检查其是否有 right child， 如果有，把 right child 当做 root，一路向左，push in stack。
    
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
Postorder 是最复杂的，有两种实现方法，一种用两个stack，另一种只用一个。这里提供只用一个stack的。
    * 基本思想是先像 inorder 一样把node一路向左 push in stack， 然后对每个stack.peek node，先检查它是否有 right child，如果没有，则 pop 然后 visit it， 如果有，检查是否是刚刚 visited 的，如果是，则说明 this node 的左右 child 都完成了，则pop，让curr = that node。
    
```java
    // java
    private void helper(TreeNode root) {
        Deque<TreeNode> stack = new LinkedList<>();
        TreeNode curr = root;
        while (curr != null) {
            stack.add(curr);
            curr = curr.left;
        }
        while (stack.size() > 0) {
            TreeNode node = stack.peekLast();
            if (node.right == null || node.right == curr) {
                // 如果没有 right child 或者 rc 是刚刚 visited 的，则 pop and visit
                curr = stack.removeLast();
                System.out.print(" " + curr.val);
            } else {
                // 如果有 rc 并且没有被 visited 过，则要一路向左 add in stack
                node = node.right;
                while (node != null) {
                    stack.add(node);
                    node = node.left;
                }
            }
        }
    }
```

