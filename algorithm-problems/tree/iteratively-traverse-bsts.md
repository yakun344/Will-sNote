# Iteratively Traverse BSTs

_update Jun 21, 2017_

**LeetCode problems:**\
[Inorder](https://leetcode.com/problems/binary-tree-inorder-traversal/description/)\
[Preorder](https://leetcode.com/problems/binary-tree-preorder-traversal/description/)\
[Postorder](https://leetcode.com/problems/binary-tree-postorder-traversal/description/)\
\


这里所讲的`BST Traversal`方法指的主要是`preorder, inorder, postorder`，严格意义上三种都属于`DFS`。用`recursion`做BST的`Traversal`比较简单，思路很直接，但是如果要用`Iterative`的方法，就要用到`stack`，没有那么直观。这里主要提供三种遍历的`java`实现。

## Preorder

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

## Inorder

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

## Postorder

这道题目在LeetCode里居然是hard。。这或许是我见过的最草率的hard题目了，233333；[LeetCode](https://leetcode.com/problems/binary-tree-postorder-traversal/description/)

Postorder 是最复杂的，有两种实现方法，一种用两个stack，另一种只用一个。这里提供只用一个stack的。

基本思想是先像 inorder 一样把node一路向左 push in stack。然后每次检查`stack.peek`，如果`peek` 没有`right child` 或者 `peek.right == prev`即右孩子刚刚 visited 过，说明该node的右子树刚刚被visit，则 visit 这个 peek, 将其出栈，并把它赋给 prev。如果`peek`有`right child`且没有 visited 过，则把 right 当做root，一路向左 push in stack。

```java
    class Solution {
        public List<Integer> postorderTraversal(TreeNode root) {
            List<Integer> res = new ArrayList<>();
            Deque<TreeNode> stack = new LinkedList<>();
            // 先一路向左push进stack
            while (root != null) {
                stack.addLast(root);
                root = root.left;
            }
            // prev 来跟踪之前一个被visit的node
            TreeNode prev = null;
            while (! stack.isEmpty()) {
                TreeNode node = stack.peekLast();
                // 如果栈顶node没有右孩子，或者右孩子刚被visit过，则应该visit栈顶node，
                // 之后令 prev = 该node
                if (node.right == null || node.right == prev) {
                    res.add(node.val);
                    prev = node;
                    stack.removeLast();
                } else {
                    // 如果栈顶右孩子还没有被visit，则进入右子树，开始一路向左push
                    node = node.right;
                    while (node != null) {
                        stack.addLast(node);
                        node = node.left;
                    }
                }
            }
            return res;
        }
    }
```

_注意： 这个方法是可以求该 BST 的 max hight 的，就是 stack 的最大 size_ \


_update Jan 14,2018 0:59_

## Update

**Postorder traversal, two stacks solution**

代码比较简单，但是不太好描述，可以画图理解。

```java
class Solution {
    public List<Integer> postorderTraversal(TreeNode root) {
        Deque<TreeNode> stack1 = new LinkedList<>();
        Deque<TreeNode> stack2 = new LinkedList<>();
        List<Integer> res = new ArrayList<>();
        if (root == null) return res;
        stack1.offerFirst(root);
        while (! stack1.isEmpty()) {
            TreeNode curr = stack1.pollFirst(); // 这里是关键，每次将当前1栈顶移到2栈
            stack2.offerFirst(curr);
            if (curr.left != null) stack1.offerFirst(curr.left);
            if (curr.right != null) stack1.offerFirst(curr.right);
        }
        while (! stack2.isEmpty()) res.add(stack2.pollFirst().val);
        return res;
    }
}
```

需要注意的是这种思路虽然也work，但是性能非常不好，因为要完成 postorder traversal，这种思路需要先完成所有的遍历，之后才能开始输出节点，所以更加推荐之前的那种。
